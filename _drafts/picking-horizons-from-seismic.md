---
layout: article
title: Picking Horizons From Seismic
modified:
categories: pyrus
excerpt: Interpreting the Base Cretaceous Unconformity on Equinor's Volve seismic with multigrid horizon extraction in Pyrus, and checking the result against the official interpretation.
tags: [modelling_surfaces, seismic_interpretation, horizon_picking, volve, mhe, pyrus_suite, code_snippet]
image:
  feature: feature-picking-horizons-from-seismic-1024x256.jpg
  teaser: teaser-picking-horizons-from-seismic-400x250.jpg
  linkedin: teaser-picking-horizons-from-seismic-400x250.jpg
  thumb:
comments: false
mathjax: true
---

<!-- DRAFT. Placeholders are marked TODO. Figures follow the shot list; statistics come from the MHE3 vs official comparison. -->

A seismic horizon is the interpreter's way of saying "this reflection is the same rock surface everywhere". Pick it on a few lines, join the dots, and you have a map of the subsurface. In practice the dots refuse to join: the reflection fades, splits, crosses a fault and comes back a cycle higher or lower, and the software that was meant to follow it wanders off after a stronger neighbour. I have spent more hours than I care to admit nudging autotracked horizons back onto the right peak, so naturally I wanted to see whether I could do better in Pyrus.

This post is part tutorial and part experiment. The tutorial half walks through interpreting a horizon in Pyrus from the first pick to a saved surface. The experiment half asks the obvious question: how close does the result come to an interpretation done properly, by the people who drilled the field?

For both, I've used Equinor's **Volve** data set. Volve is a small oil field in the Norwegian North Sea that produced from 2008 until 2016, and in 2018 Equinor released essentially everything about it: seismic, wells, production data and, usefully for this post, the geophysical interpretations (Equinor, 2018). That makes it one of the very few places where you can compare your own horizon against a real one and publish the pictures.

The horizon is the **Base Cretaceous Unconformity (BCU)**. It is one of the classic North Sea reflectors: regionally extensive, usually strong, and interpreted on almost every survey in the basin. It's a fair test: easy enough that a tracker should cope, but with enough faulting and changes in character to catch out a lazy one.

## What Is a Horizon, Anyway?

To the software, a horizon is just a surface: one two-way time (or depth) for each trace in the survey, $$z(x, y)$$, sitting on the same inline/crossline lattice as the seismic. In Pyrus it is stored like every other surface, as a regular grid (the subject of the first post in this series), so once it exists it can be coloured, contoured, exported and compared like any other surface.

The hard part is getting the numbers in the first place. An interpreter can see the reflection; the software has to be told what "the same reflection" means.

## Why Not Just Follow the Peak?

The obvious approach is the one most of us learned first. Pick a point on a peak (or trough), step to the next trace, look a few samples up and down for the nearest peak, and repeat. It is simple, fast, and works beautifully right up until it doesn't.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-peak-tracker-failure.png" data-lightbox="image-1" data-title="A peak-following tracker jumps a cycle across a fault and carries on happily on the wrong reflection.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-peak-tracker-failure.png" alt="A peak-following tracker jumps a cycle across a fault and carries on happily on the wrong reflection."/>
	</a>
	<figcaption><strong>Figure 1: A peak-following tracker jumps a cycle across a fault and carries on happily on the wrong reflection.</strong></figcaption>
</figure>
<!-- TODO Figure 1: optional. Either a real example on Volve or a schematic. -->

The trouble is that every decision is local and final. Once the tracker has stepped onto the wrong cycle, every later trace agrees with the mistake, because each one only compares itself with its neighbour. Errors don't average out; they propagate. Faults, noise and places where the reflection dims are exactly where a human looks hardest and a peak-follower looks least.

## What Works Better?

The method in Pyrus comes from Wu and Fomel (2018), who turned horizon picking from a sequence of local decisions into one global fit. Instead of asking "where is the peak on the next trace?", it asks "which surface best agrees with the dip of the reflections everywhere, while passing through the interpreter's picks?"

The first ingredient is the **local slope** of the reflections. At every sample of the seismic, a structure-tensor filter estimates how steeply the reflections dip in the inline and crossline directions, in samples per trace. A horizon that follows the reflections must have the same dip as the seismic at every point it passes through, which gives a pair of equations for each trace:

$$\frac{\partial z}{\partial x} = p(x, y, z), \qquad \frac{\partial z}{\partial y} = q(x, y, z)$$

Where:

 - _z_ = horizon two-way time at trace (_x_, _y_) (samples)
 - _x_, _y_ = inline and crossline trace positions (traces)
 - _p_, _q_ = local reflection slopes in the inline and crossline directions, evaluated on the horizon (samples per trace)

There are far more equations than unknowns, and the slopes are noisy, so the horizon is found by least squares: the surface whose gradient matches the slopes as closely as possible, with the picks held fixed. Because the slopes themselves depend on where the surface is, the fit is iterated: evaluate the slopes on the current surface, solve, and repeat.

Slopes on their own have the same weakness as the peak-follower, only spread more evenly: integrate a small slope error over hundreds of traces and the surface drifts, and across a fault the slope says nothing useful at all. The second ingredient fixes this. **Multigrid correlations** cross-correlate traces that are not neighbours, but several traces apart on a series of coarser grids, and add the resulting time shifts as extra equations. These long-range links tie distant parts of the surface together directly, so they can bridge faults and stop slow drift.

Everything is solved together, so a doubtful patch is pulled into line by the good data around it instead of dragging everything after it off course. That is the essential difference from the peak-follower.

## How Does Pyrus Do It?

Pyrus uses an adapted version of Xinming Wu's open-source implementation, reworked to read the seismic out of core from the HDF5 cube. A horizon is built in three stages, each feeding the next:

 1. **Picks.** You click on the seismic. Each pick is recorded against its inline, crossline and sample.
 2. **Horizon lines.** For each pick, the 2D version of the extractor (`HorizonExtractor2`) tracks the horizon along the whole inline or crossline through it, and along any existing lines it intersects. These lines are what you see appear as you pick.
 3. **The surface.** The 3D extractor (`HorizonExtractor3`, or MHE3) fits the whole surface to the picks and to samples taken from the horizon lines.

The surface stage starts from an initial guess, and the quality of the guess matters. Inside the area covered by the picks, Pyrus triangulates the picks and line samples and interpolates them with cubic Bézier patches built from Akima derivatives (Akima, 1970). Outside that area it uses a smooth biharmonic radial basis function, which extrapolates gently instead of wildly. The two are blended with a Gaussian weight on the distance from the convex hull of the picks. The blending loop is:

```java
        // Calculate convex hull that envelopes the picks.
        List<double[]> hull = AdvMath.computeConvexHull2D(AdvMath.toXY(pcb.getAllPoints()));
        float[][] hs_blend = new float[N3][N2];
        double sigma = 20.0; // smoothing radius in samples
        for (int i3 = 0; i3 < N3; i3++) {
            for (int i2 = 0; i2 < N2; i2++) {
                if (cancelled()) {
                    return;
                }
                double x = i3 + i3lo;
                double y = i2 + i2lo;
                double d = AdvMath.hullPointDist(hull, new double[]{x, y});
                double w = Math.exp(-(d * d) / (sigma * sigma));

                // Blend generalised and RBF surfaces
                hs_blend[i3][i2] = (float) (w * hs[i3][i2] + (1.0 - w) * hs_rbf[i3][i2]);
            }
        }
```

That blended surface is the quick **preview** you see while picking. The full **detailed** surface hands it to MHE3 as the starting point and refines it against the slopes and correlations. The slopes are computed lazily, only for the parts of the cube the solver actually visits, which keeps memory in check on large surveys:

```java
        LocalSlopeFinder lsf = new LocalSlopeFinder(horizon_sigma1, horizon_sigma2, horizon_sigma2, horizon_pmax);
        LazySlopeVolume3D slopes = new LazySlopeVolume3D(vf, lsf);

        // Initialise surface with seed point (this uses ROI coordinates).
        HorizonExtractor3 he3 = new HorizonExtractor3();
        he3.setSmoothings(horizon_smoothing, horizon_smoothing);
        he3.setWeights(0.001f);
        he3.setExternalIterations(10);
```

All of this runs as background tasks with generation counters, so you can keep picking while a surface is being computed, and a stale result never overwrites a newer one.

## Interpreting the BCU on Volve, Step by Step

This is the practical part. The steps below are how I interpreted the BCU on Volve; the same steps apply to any horizon on any survey.

### 1. Load the Seismic

Load the Volve seismic cube in two-way time (Pyrus converts the SEG-Y to an HDF5 cube on first import, so later sessions open quickly). Set a vertical exaggeration that makes the reflections easy to follow; the **VE** spinner in the editor panel goes from 1 to 10.

<!-- TODO: confirm which Volve seismic volume was used (survey name, time or depth, sample rate). -->

Move the inline and crossline planes through the cube with the arrow keys (left/right for inlines, up/down for crosslines) and the time slice with Page Up and Page Down. Before picking anything, scan through the survey and get a feel for where the BCU is strong, where it weakens and where the faults are. Five minutes spent looking saves an hour spent fixing.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-inline.png" data-lightbox="image-2" data-title="An inline through Volve with the BCU marked. TODO: inline number.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-inline.png" alt="An inline through Volve with the BCU marked. TODO: inline number."/>
	</a>
	<figcaption><strong>Figure 2: An inline through Volve with the BCU marked. TODO: inline number.</strong></figcaption>
</figure>

### 2. Start a New Horizon

Click **Digitise Horizon** to open the picking panel and enter horizon editing mode. Give the horizon a name in **Horizon Information** (I used "BCU (MHE3)") and choose its line, marker and surface colours.

Under **Pick Options**, choose **2D Horizon Lines Only** to pick on inlines and crosslines, or **2D Lines and 3D Points** to allow picks on time slices as well. Start with lines only; time-slice picks are useful later to pin down awkward areas.

### 3. Set the Autopick Constraints

The **Autopick Horizon Constraints** control how the slopes are measured and how smooth the surface is. The defaults suit most data, so I'd only change them once you've seen a result you don't like.

| Control | Default | What it does | When to change it |
| --- | --- | --- | --- |
| Smoothing Half-Width: Down | 8 samples | Vertical extent of the window used to measure the reflection slopes | Increase for noisy data; decrease if thin, closely spaced reflections are being blurred together |
| Smoothing Half-Width: Across | 2 traces | Lateral extent of the same window | Increase on noisy data with gentle structure; keep small near faults |
| Maximum Slope | 3 samples/trace | Steepest dip the slope estimate will accept | Increase for steeply dipping horizons; the default is generous for most |
| Smoothing | 6 | How strongly the 3D surface is smoothed during the fit | Lower for more detail; higher for a calmer surface on noisy data |
| Trace Padding | 64 traces | How far the surface extends beyond the area covered by picks | Increase to extrapolate further; decrease to keep the surface close to the picks |

<!-- TODO: add the values used for Volve if they differ from the defaults. -->

### 4. Pick a Few Lines

Click on the BCU on an inline. Pyrus records the pick and tracks the horizon along the whole line; the status bar shows the line, the world and cube coordinates and the two-way time of the pick. Check the tracked line from end to end. Where it strays, click on the correct reflection to add a pick there and the line is re-tracked through it. **Shift-click** a pick to delete it, and **Undo** (Ctrl+Z) reverses the last edit.

<!-- TODO: confirm Undo is Ctrl+Z / Edit > Undo in the Subsurface 3D editor. -->

Then do the same on a crossline. A pick on one line also re-tracks the existing lines it crosses, so a sparse grid of lines quickly ties together. On Volve I picked TODO inlines and TODO crosslines, spaced roughly TODO lines apart, with extra picks either side of the main faults.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-lines.png" data-lightbox="image-3" data-title="Picks and tracked horizon lines on a grid of Volve inlines and crosslines.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-lines.png" alt="Picks and tracked horizon lines on a grid of Volve inlines and crosslines."/>
	</a>
	<figcaption><strong>Figure 3: Picks and tracked horizon lines on a grid of Volve inlines and crosslines.</strong></figcaption>
</figure>

A few well-placed picks are worth more than many careless ones. The slopes do the heavy lifting between picks; your job is to tell the solver where the horizon is wherever the slopes alone can't, mostly across faults and through dim patches.

### 5. Preview the Surface

Tick **Show surface** to see the quick preview surface. It updates as you pick, and the **Surface Opacity** slider lets you see the seismic through it. The preview is good enough to spot gross errors, such as a line on the wrong cycle, which show up as a step or a fold in the surface.

### 6. Extract the Detailed Surface

When the lines look right, click **Extract 3D Horizon Surface**. This runs the full MHE3 refinement over the picked area plus the trace padding. It takes longer than the preview; progress shows in the status bar, and the task can be cancelled. TODO: extraction time on Volve.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-surface.png" data-lightbox="image-4" data-title="The detailed MHE3 BCU surface on Volve, coloured by two-way time with contours.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-bcu-surface.png" alt="The detailed MHE3 BCU surface on Volve, coloured by two-way time with contours."/>
	</a>
	<figcaption><strong>Figure 4: The detailed MHE3 BCU surface on Volve, coloured by two-way time with contours.</strong></figcaption>
</figure>

### 7. Check It

Quality control is the part that gets skipped, so it deserves its own step. Move the inline, crossline and time-slice planes through the surface and check that it sits on the reflection everywhere, not just near the picks. Contours help: tight, tangled contours usually mean the surface has jumped a cycle, and a straight contour running along a fault means the surface is smearing across it. Where you find a problem, add a pick there and extract again.

### 8. Save and Export

Click **Save Horizon** to save the surface into the project as a `.vtkhdf` file. From then on it behaves like any other horizon: it can be coloured and contoured from the property sheet, and exported to ASCII XYZ (or other formats) with **Export As...**.

## How Does It Compare With the Official Interpretation?

The Volve release includes Equinor's own interpretation of the BCU. Theirs was done by interpreters who knew the field and had the wells, so it is the closest thing to a right answer available, though it is worth remembering that it is still an interpretation, not ground truth.

I imported it with the ASCII horizon importer from the second post, which works out the survey lattice from the points themselves. This is where the comparison found a bug before it found any differences: the first import came in mirrored across the survey, because the lattice estimator and the axes it built disagreed about which way the j-axis pointed. An export routine had quietly been mirroring everything back, so round trips looked perfect and the error only showed against someone else's data. A horizon in the wrong place is a fine motivation for checking your handedness conventions.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-official-mirrored.png" data-lightbox="image-5" data-title="The official BCU imported before the handedness fix, mirrored across the survey.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-official-mirrored.png" alt="The official BCU imported before the handedness fix, mirrored across the survey."/>
	</a>
	<figcaption><strong>Figure 5: The official BCU imported before the handedness fix, mirrored across the survey.</strong></figcaption>
</figure>
<!-- TODO Figure 5: optional; would need the fix reverted temporarily to recreate. Drop the figure if not. -->

With that fixed, the two surfaces can be overlaid. Setting both to partial opacity in the property sheet, in contrasting colours, makes the differences obvious: where the surfaces agree the colours mix, and where they don't, one shows through the other.

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-overlay.png" data-lightbox="image-6" data-title="The MHE3 BCU and the official BCU overlaid, both partly transparent.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-overlay.png" alt="The MHE3 BCU and the official BCU overlaid, both partly transparent."/>
	</a>
	<figcaption><strong>Figure 6: The MHE3 BCU and the official BCU overlaid, both partly transparent.</strong></figcaption>
</figure>

An overlay shows where the surfaces differ; numbers say by how much. Both horizons sit on the same survey lattice, so the comparison is simply the difference, MHE3 minus official, at every trace where both are defined:

$$\Delta t(x, y) = t_{\mathrm{MHE3}}(x, y) - t_{\mathrm{official}}(x, y)$$

Where:

 - <span><i>&Delta;t</i></span> = time difference at trace (_x_, _y_) (ms)
 - <span><i>t</i><sub>MHE3</sub></span> = two-way time of the MHE3 horizon (ms)
 - <span><i>t</i><sub>official</sub></span> = two-way time of the official horizon (ms)

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-difference-map.png" data-lightbox="image-7" data-title="Difference map, MHE3 minus official BCU, in milliseconds, with a diverging colour map centred on zero.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-difference-map.png" alt="Difference map, MHE3 minus official BCU, in milliseconds, with a diverging colour map centred on zero."/>
	</a>
	<figcaption><strong>Figure 7: Difference map, MHE3 minus official BCU, in milliseconds, with a diverging colour map centred on zero.</strong></figcaption>
</figure>

The summary statistics are:

| Measure | Value |
| --- | --- |
| Traces compared | TODO |
| Mean difference (bias) | TODO ms |
| Standard deviation | TODO ms |
| RMS difference | TODO ms |
| 90th percentile of &#124;&Delta;t&#124; | TODO ms |
| Within &plusmn;1 sample (TODO ms) | TODO % |
| Largest difference | TODO ms |

<figure>
	<a href="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-difference-histogram.png" data-lightbox="image-8" data-title="Histogram of the time differences between the MHE3 and official BCU.">
		<img src="{{ site.url }}/images/Pyrus/modelling-surfaces/picking-volve-difference-histogram.png" alt="Histogram of the time differences between the MHE3 and official BCU."/>
	</a>
	<figcaption><strong>Figure 8: Histogram of the time differences between the MHE3 and official BCU.</strong></figcaption>
</figure>

<!-- TODO: interpretation of the results. Points worth covering once the numbers are in:
     - Is there a bias (consistent offset)? If so, it may be a different leg of the wavelet (peak vs zero crossing), not an error.
     - Where are the large differences: near faults, at the survey edges, in dim areas? Show one inline through the worst area with both horizons on it.
     - Is the official surface smoother (gridded or smoothed after picking) or rougher than MHE3?
     - Which one do you believe at the worst spot, looking at the seismic? -->

TODO: discussion of the differences.

## What Did I Learn?

TODO: conclusions once the comparison is done. Candidates:

 1. A handful of picks in the right places (across faults, through dim zones) does more than many picks on easy ground.
 2. Solving for the whole surface at once is more forgiving than tracking trace by trace, because a bad patch is outvoted by the good data around it.
 3. Checking against someone else's interpretation finds bugs that self-consistent round trips never will.

The one-line lesson: let the seismic dips do the tracking, and spend your picks where the dips can't be trusted.

## References

 - Akima, H. 1970. A New Method of Interpolation and Smooth Curve Fitting Based on Local Procedures. _J. ACM_ **17** (4): 589&ndash;602. [https://doi.org/10.1145/321607.321609](https://doi.org/10.1145/321607.321609).
 - Equinor. 2018. Disclosing all Volve data. [https://www.equinor.com/news/archive/14jun2018-disclosing-volve-data](https://www.equinor.com/news/archive/14jun2018-disclosing-volve-data).
 - Wu, X. and Fomel, S. 2018. Least-Squares Horizons With Local Slopes and Multigrid Correlations. _Geophysics_ **83** (4): IM29&ndash;IM40. [https://doi.org/10.1190/geo2017-0830.1](https://doi.org/10.1190/geo2017-0830.1).
