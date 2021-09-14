---
layout: article
title: Relative Permeability Model
modified:
categories: analysis
excerpt: Generating physically consistent relative permeability curves for reservoir simulation based on a simple rock model and fluid EoS.
tags: [static modelling, rock characterisation, rock properties, relative permeability, hysteresis, wettability, miscibility, saturation endpoints, reservoir simulation]
image:
  feature:
  teaser: teaser-rel-perm-model-400x250.png
  thumb:
comments: true
---

A common approach is for laboratories to use special core analysis (SCAL) techniques on reservoir core samples to determine these relationships. A problem with this approach is that even with enormous budgets, only a small section of the reservoir rock can be sampled and the measured results may therefore not be applicable to all of the reservoir. If no core samples are available, then a method to generate physically reasonable and consistent synthetic relative permeability curves is needed. Relative permeability data can also be estimated by history matching production data in a reservoir simulator. It is noted by several authors that there is often a poor match between the history-matched relative permeability curves and those obtained from core measurements. This only serves to highlight the different scales used to derive the curves i.e., microscopic scale for core-derived curves and macroscopic scale for history-derived curves. My personal view is that core data should be used to validate general relationships, not to derive reservoir properties as gospel. Excessively constraining the relationships to match core data in isolation is akin to letting the tail wag the dog.

Several methods have been published in the literature to describe how relative permeability curves can be constructed. In general, these approaches involve fitting of curves to laboratory data and there are few, if any, published approaches to generating generalised curves for specific rock and fluid type combinations. The method described in the following sections has been devised to match an unpublished carbonate field dataset and has not been peer reviewed. Despite this shortcoming, the method does appear to be capable of generating reasonable curves that should be useful where no other data is available.

## Two-Phase Relative Permeability

Absolute permeability is a property of the porous medium e.g., reservoir rock, and measures the medium’s capacity for fluid flow. Permeability is often measured using a single fluid type, such as air, and its value is equally applicable to other single phase fluid systems flowing through the porous medium. In the presence of two or more immiscible fluids, this relationship no longer holds as the fluid with the higher saturation will preferentially flow. Relative permeability describes the ability of one fluid to flow in the presence of one or more other fluids. The simplest form of relative permeability is therefore the relative flow between two fluids. Relative permeability is simply defined as the fractional permeability of a phase relative to the absolute permeability:

![k_{rp}=\frac{k_{p}}{k}](https://latex.codecogs.com/gif.latex?k_{rp}=\frac{k_{p}}{k})

Where:

k<sub>rp</sub> = Phase ‘p’ relative permeability in presence of other phases, fraction<br>
k<sub>p</sub> = Phase ‘p’ permeability in presence of other phases, millidarcy<br>
k = Absolute permeability, millidarcy

Simulators typically consider the oil-water and gas-oil relative permeability systems. The gas-water system is derived from three-phase relative permeability using these two systems.

There are several elements that need to be captured when modelling two-phase relative permeability curves. The most important aspect is that any curves must be entirely consistent with all other modelled parameters. This means that endpoints must be derived using similar approaches to those used for saturation height modelling etc. Whilst the curves used are not derived from laboratory measurements, the form of the curves should also be consistent with those that are obtained from core measurements. Both drainage and imbibition curves should be derived in a manner where both curves are compatible with each other. Finally, consideration must be given to whether the rock is water-wet or oil-wet.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/oil-water-rel-perm-curve.png" data-lightbox="image-1" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/oil-water-rel-perm-curve.png" alt="." />
	</a>
	<figcaption><strong>Figure 1: .</strong></figcaption>
</figure>

Starting with 100% water saturation (S<sub>w</sub> = 1.0) the relative permeability to water is 1.0 and to oil is zero. As the oil saturation increases the oil relative permeability increases and the water relative permeability decreases following the drainage lines until the irreducible water saturation (S<sub>wirr</sub>) is reached. At this point the relative permeability to oil at the critical water saturation (k<sub>rocw</sub>) is reached and water relative permeability is zero.

Note that oil does not flow until oil saturation is above a small critical oil saturation (S<sub>ocr</sub>). This is not explicitly modelled as the use of LET curves allows some curvature in the lower part of the gas relative permeability curve and the upper part of the oil relative permeability curve which captures this phenomenon. Similarly, the irreducible water saturation, or the minimum water saturation below which water cannot be further reduced, is close to the critical water saturation (S<sub>wcr</sub>) which is the saturation above which water begins to flow. As with the S<sub>ocr</sub>, this is not explicitly modelled but the use of LET curves allows curvature in the lower part of the water relative permeability curve which captures the phenomenon.

The relative permeability curves exhibit hysteresis, so as water saturation begins to increase again above the critical water saturation (S<sub>wcr</sub>), an imbibition line is followed. Under circumstances of increasing water saturation, the relative permeability to oil gradually decreases, and the relative permeability to water increases, until the residual oil saturation in the presence of water is reached (S<sub>orw</sub>). Note that the relative permeability to water at the residual oil saturation (k<sub>rwro</sub>) is less than 1.0.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/gas-oil-rel-perm-curve.png" data-lightbox="image-2" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/gas-oil-rel-perm-curve.png" alt="." />
	</a>
	<figcaption><strong>Figure 2: .</strong></figcaption>
</figure>

For the gas-oil system, starting with 100% oil saturation the initial relative permeability to gas is zero and to oil is k<sub>rocw</sub>, as gas saturation increases and displaces oil, the relative permeabilities follow their respective drainage curves until the maximum gas saturation allowing for irreducible water and residual oil is reached at 1.0 – (S<sub>wirr</sub> + S<sub>org</sub>). Whilst this is a two phase relative permeability curve for gas and oil, because reservoir rock will always contain irreducible water, its presence is also considered when calculating the gas saturation. Note that gas does not flow until gas saturation is above a small critical gas saturation (S<sub>gcr</sub>). This is not explicitly modelled as the use of LET curves allows some curvature in the lower part of the gas relative permeability curve and the upper part of the oil relative permeability curve which captures this phenomenon.

Since we might also have a situation where there is just gas and water (i.e. no residual oil saturation) an imbibition curve is modelled starting from 1.0 – S<sub>wirr</sub>. This allows the relative permeability to condensate that is increasing in the reservoir to be captured, with initial relative permeability to gas at the critical water saturation of k<sub>rgcw</sub> decreasing along with gas saturation until the irreducible liquid saturation (irreducible water saturation plus residual oil saturation) is reached with relative permeability to gas of k<sub>rgrl</sub>. As gas saturation decreases below this point, hysteresis is observed up to a minimum gas saturation being the residual gas saturation S<sub>grw</sub>. Note that residual gas saturation in the presence of water is used as this is assumed to be the same as the residual gas saturation in the presence of oil. The relative permeability to oil at the residual gas saturation is k<sub>rorg</sub>.

## LET Correlations

Pyrus generates relative permeability curves following the LET method. Use of a LET correlation allows all required relationships and behaviours to be implemented. This is a generalised fitting approach that takes three parameters to control the lower part of the curve (L), the position of elevation of the curve (E) and the upper part or top of the curve (T). Whilst the acronym for the method appears to be an amalgamation of the parts of the curve being modelled e.g. **L**ower, **E**levation and **T**op, it is more likely that this is actually derived from the author’s surnames being **L**omeland, **E**beltoft and **T**homas.

Essentially this method works by fitting “S” or “J” curves between specified endpoints. Provided the endpoints can be specified appropriately, then physically reasonable relative permeability curves can be generated. An attractive aspect of this approach is that the generated curves are smoothly continuous over the entire saturation range. In this regard they are considered superior to other common correlations used in industry such as Corey curves.

There are several relative permeability curves in total to account for both drainage and imbibition curves in oil-water and gas-oil systems. For oil-water systems, we must additionally account for water-wet and oil-wet rock. These curves are defined as:

**Drainage Oil-Water System**
{% raw %}
![k_{row}=\frac{k_{rocw}\left(1-S_{e}\right)^{L_{o}^{w}}}{\left(1-S_{e}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{e}}^{T_{o}^{w}}}}](https://latex.codecogs.com/gif.latex?k_{row}=\frac{k_{rocw}\left(1-S_{e}\right)^{L_{o}^{w}}}{\left(1-S_{e}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{e}}^{T_{o}^{w}}}})

![k_{rwo}\textrm{(water-wet)}=\frac{k_{rwt}{S_{e}}^{L_{w}^{o}}}{{S_{e}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{e}\right)^{T_{w}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rwo}\textrm{(water-wet)}=\frac{k_{rwt}{S_{e}}^{L_{w}^{o}}}{{S_{e}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{e}\right)^{T_{w}^{o}}}})

![k_{rwo}\textrm{(oil-wet)}=\frac{k_{rwt}{S_{wn}}^{L_{w}^{o}}}{{S_{wn}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{T_{w}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rwo}\textrm{(oil-wet)}=\frac{k_{rwt}{S_{wn}}^{L_{w}^{o}}}{{S_{wn}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{T_{w}^{o}}}})
{% endraw %}
Where:

k<sub>row</sub> = Oil relative permeability in presence of water, fraction<br>
k<sub>rwo</sub> = Water relative permeability in presence of oil, fraction<br>
k<sub>rocw</sub> = Oil relative permeability at critical water saturation, fraction<br>
k<sub>rwt</sub> = Total water relative permeability, fraction<br>
S<sub>e</sub> = Effective water saturation, fraction<br>
S<sub>wn</sub> = Normalised water saturation, fraction<br>
L<sub>o</sub><sup>w</sup> = Lower empirical parameter for oil phase with associated water phase, dimensionless<br>
E<sub>o</sub><sup>w</sup> = Elevation empirical parameter for oil phase with associated water phase, dimensionless<br>
T<sub>o</sub><sup>w</sup> = Top empirical parameter for oil phase with associated water phase, dimensionless<br>
L<sub>w</sub><sup>o</sup> = Lower empirical parameter for water phase with associated oil phase, dimensionless<br>
E<sub>w</sub><sup>o</sup> = Elevation empirical parameter for water phase with associated oil phase, dimensionless<br>
T<sub>w</sub><sup>o</sup> = Top empirical parameter for water phase with associated oil phase, dimensionless

**Imbibition Oil-Water System**
{% raw %}
![k_{row}=\frac{k_{rocw}\left(1-S_{wn}\right)^{L_{o}^{w}}}{\left(1-S_{wn}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{wn}}^{T_{o}^{w}}}}](https://latex.codecogs.com/gif.latex?k_{row}=\frac{k_{rocw}\left(1-S_{wn}\right)^{L_{o}^{w}}}{\left(1-S_{wn}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{wn}}^{T_{o}^{w}}}})

![k_{rwo}\textrm{(water-wet)}=\frac{k_{rwro}{S_{wn}}^{L_{w}^{o}}}{{S_{wn}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{T_{w}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rwo}\textrm{(water-wet)}=\frac{k_{rwro}{S_{wn}}^{L_{w}^{o}}}{{S_{wn}}^{L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{T_{w}^{o}}}})

![k_{rwo}\textrm{(oil-wet)}=\frac{k_{rwro}{S_{wn}}^{0.9L_{w}^{o}}}{{S_{wn}}^{0.9L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{1.1T_{w}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rwo}\textrm{(oil-wet)}=\frac{k_{rwro}{S_{wn}}^{0.9L_{w}^{o}}}{{S_{wn}}^{0.9L_{w}^{o}}+E_{w}^{o}\cdot{\left(1-S_{wn}\right)^{1.1T_{w}^{o}}}})
{% endraw %}
Where:

k<sub>row</sub> = Oil relative permeability in presence of water, fraction<br>
k<sub>rwo</sub> = Water relative permeability in presence of oil, fraction<br>
k<sub>rocw</sub> = Oil relative permeability at critical water saturation, fraction<br>
k<sub>rwro</sub> = Water relative permeability at residual oil saturation, fraction<br>
S<sub>wn</sub> = Normalised water saturation, fraction<br>
L<sub>o</sub><sup>w</sup> = Lower empirical parameter for oil phase with associated water phase, dimensionless<br>
E<sub>o</sub><sup>w</sup> = Elevation empirical parameter for oil phase with associated water phase, dimensionless<br>
T<sub>o</sub><sup>w</sup> = Top empirical parameter for oil phase with associated water phase, dimensionless<br>
L<sub>w</sub><sup>o</sup> = Lower empirical parameter for water phase with associated oil phase, dimensionless<br>
E<sub>w</sub><sup>o</sup> = Elevation empirical parameter for water phase with associated oil phase, dimensionless<br>
T<sub>w</sub><sup>o</sup> = Top empirical parameter for water phase with associated oil phase, dimensionless

For a water-wet system where S<sub>wn</sub> = 1, then set k<sub>rwo</sub> (imbibition) to k<sub>rwo</sub> (drainage).

**Drainage Gas-Oil System**
{% raw %}
![k_{rgo}=\frac{k_{rgcw}\left(1-S_{e}\right)^{L_{g}^{o}}}{\left(1-S_{e}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{e}}^{T_{g}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rgo}=\frac{k_{rgcw}\left(1-S_{e}\right)^{L_{g}^{o}}}{\left(1-S_{e}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{e}}^{T_{g}^{o}}}})

![k_{rog}=\frac{k_{rocw}{S_{ln}}^{L_{o}^{g}}}{{S_{ln}}^{L_{o}^{g}}+E_{o}^{g}\cdot{\left(1-S_{ln}\right)^{T_{o}^{g}}}}](https://latex.codecogs.com/gif.latex?k_{rog}=\frac{k_{rocw}{S_{ln}}^{L_{o}^{g}}}{{S_{ln}}^{L_{o}^{g}}+E_{o}^{g}\cdot{\left(1-S_{ln}\right)^{T_{o}^{g}}}})
{% endraw %}
Where:

k<sub>rgo</sub> = Gas relative permeability in presence of oil, fraction<br>
k<sub>rog</sub> = Oil relative permeability in presence of gas, fraction<br>
k<sub>rocw</sub> = Oil relative permeability at critical water saturation, fraction<br>
k<sub>rgcw</sub> = Gas relative permeability at critical water saturation, fraction<br>
S<sub>e</sub> = Effective water saturation, fraction<br>
S<sub>ln</sub> = Normalised liquid saturation, fraction<br>
L<sub>g</sub><sup>o</sup> = Lower empirical parameter for gas phase with associated oil phase, dimensionless<br>
E<sub>g</sub><sup>o</sup> = Elevation empirical parameter for gas phase with associated oil phase, dimensionless<br>
T<sub>g</sub><sup>o</sup> = Top empirical parameter for gas phase with associated oil phase, dimensionless<br>
L<sub>o</sub><sup>g</sup> = Lower empirical parameter for oil phase with associated gas phase, dimensionless<br>
E<sub>o</sub><sup>g</sup> = Elevation empirical parameter for oil phase with associated gas phase, dimensionless<br>
T<sub>o</sub><sup>g</sup> = Top empirical parameter for oil phase with associated gas phase, dimensionless

**Imbibition Gas-Oil System**
{% raw %}
![k_{rgo}=\frac{k_{rgrl}\left(1-S_{ln'}\right)^{L_{g}^{o}}}{\left(1-S_{ln'}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{ln'}}^{T_{g}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rgo}=\frac{k_{rgrl}\left(1-S_{ln'}\right)^{L_{g}^{o}}}{\left(1-S_{ln'}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{ln'}}^{T_{g}^{o}}}})

![k_{rog}=\frac{k_{rorg}{S_{wn}}^{L_{o}^{g}}}{{S_{wn}}^{L_{o}^{g}}+E_{o}^{g}\cdot{\left(1-S_{wn}\right)}}](https://latex.codecogs.com/gif.latex?k_{rog}=\frac{k_{rorg}{S_{wn}}^{L_{o}^{g}}}{{S_{wn}}^{L_{o}^{g}}+E_{o}^{g}\cdot{\left(1-S_{wn}\right)}})
{% endraw %}
Where:

k<sub>rgo</sub> = Gas relative permeability in presence of oil, fraction<br>
k<sub>rog</sub> = Oil relative permeability in presence of gas, fraction<br>
k<sub>rgrl</sub> = Gas relative permeability at residual liquid saturation, fraction<br>
k<sub>rorg</sub> = Oil relative permeability at residual gas saturation, fraction<br>
S<sub>wn</sub> = Normalised water saturation, fraction<br>
S<sub>ln</sub> = Normalised liquid saturation, fraction<br>
L<sub>g</sub><sup>o</sup> = Lower empirical parameter for gas phase with associated oil phase, dimensionless<br>
E<sub>g</sub><sup>o</sup> = Elevation empirical parameter for gas phase with associated oil phase, dimensionless<br>
T<sub>g</sub><sup>o</sup> = Top empirical parameter for gas phase with associated oil phase, dimensionless<br>
L<sub>o</sub><sup>g</sup> = Lower empirical parameter for oil phase with associated gas phase, dimensionless<br>
E<sub>o</sub><sup>g</sup> = Elevation empirical parameter for oil phase with associated gas phase, dimensionless<br>
T<sub>o</sub><sup>g</sup> = Top empirical parameter for oil phase with associated gas phase, dimensionless

When the water saturation (1.0 minus gas saturation) is less than S<sub>wirr</sub> + S<sub>org</sub>, then set k<sub>rgo</sub> (imbibition) to k<sub>rgo</sub> (drainage), otherwise use the minimum of the LET equation for k<sub>rgo</sub> (imbibition) and k<sub>rgo</sub> (drainage).

For k<sub>rog</sub> (imbibition) we take the maximum of the LET equation for k<sub>rog</sub> (imbibition), k<sub>rog</sub> (drainage) and k<sub>rog</sub> (drainage) + difference between k<sub>rog</sub> (imbibition) and k<sub>rog</sub> (drainage) ∕ 1.1 at a gas saturation 0.01 higher. This helps the imbibition and drainage curves merge together nicely.

### Saturation Inputs

Effective water saturation removes the effect of irreducible water saturation so that effective saturation varies from 0 to 1 between irreducible water saturation and 100% water saturation. Normalised water saturation removes the effect of both irreducible water saturation and residual hydrocarbon saturation, so that normalised saturation varies from 0 to 1 between irreducible water saturation and (1 – residual hydrocarbon saturation).

![S_{e}=\frac{S_{w}-S_{wirr}}{1-S_{wirr}}](https://latex.codecogs.com/gif.latex?S_{e}=\frac{S_{w}-S_{wirr}}{1-S_{wirr}})

![S_{wn}=\frac{S_{w}-S_{wirr}}{1-S_{orw}-S_{wirr}}](https://latex.codecogs.com/gif.latex?S_{wn}=\frac{S_{w}-S_{wirr}}{1-S_{orw}-S_{wirr}})

![S_{ln}=1-\frac{1-S_{w}}{1-S_{org}-S_{wirr}}](https://latex.codecogs.com/gif.latex?S_{ln}=1-\frac{1-S_{w}}{1-S_{org}-S_{wirr}})

![S_{ln'}\textrm{(water-wet)}=\frac{S_{w}-S_{org}-S_{wirr}}{1-S_{org}-S_{grw}-S_{wirr}}](https://latex.codecogs.com/gif.latex?S_{ln'}\textrm{(water-wet)}=\frac{S_{w}-S_{org}-S_{wirr}}{1-S_{org}-S_{grw}-S_{wirr}})

![S_{ln'}\textrm{(oil-wet)}=\frac{S_{w}-S_{org}-S_{wirr}}{1-S_{org}-S_{gro}-S_{wirr}}](https://latex.codecogs.com/gif.latex?S_{ln'}\textrm{(oil-wet)}=\frac{S_{w}-S_{org}-S_{wirr}}{1-S_{org}-S_{gro}-S_{wirr}})

### LET Parameters

The LET empirical parameters have been determined through trial and error experimentation to generate curves that resemble actual core measured data. Whilst these could probably be improved, the basis for modelling the curves is that the endpoints and behaviour of the curves is more critical, and the precise shape of the curves is of second order importance.

|| Oil-Water | Water-Oil | Oil-Gas | Gas-Oil |
|---|---|---|---|---|
| Lower parameter (L) | 1.5 × β | L<sub>o</sub><sup>w</sup> | 1.25 + β / 2 × σ<sub>go</sub> / (0.15 + σ<sub>go</sub>) | L<sub>o</sub><sup>g</sup> |
| Elevation parameter (E) | L<sub>o</sub><sup>w</sup> | L<sub>o</sub><sup>w</sup> / 2 | L<sub>o</sub><sup>g</sup> | MAX(1, L<sub>g</sub><sup>o</sup> / 2) |
| Top parameter (T) | IF(θ<sub>ow</sub> > 90°, 1, 2) | IF(θ<sub>ow</sub> > 90°, 2, 0.9) | 1.25 + 0.75 × σ<sub>go</sub> / (0.15 + σ<sub>go</sub>) | 1.1 |

### Curve Endpoints

First let’s consider the saturation endpoints.

-   **S<sub>wirr</sub> or S<sub>wcr</sub>:** This can be obtained from the Holmes-Buckles equation and is related to the pore throat size relationship to the pore size.
-   **S<sub>grw</sub>:** The residual gas saturation after water invasion can be obtained from the Holtz-Land equation. An additional constraint must be considered where the value used cannot be larger than 0.9 × (1.0 − S<sub>wirr</sub>).
-   **S<sub>orw</sub>:** For an oil-wet reservoir with a contact angle θ<sub>ow</sub> > 90°, we set the residual oil saturation to the same value calculated for the irreducible water saturation. In other words, if oil is the wetting phase, then it should behave similarly to water as the wetting phase. For a water-wet reservoir, we set the residual oil saturation to the same value as the residual gas saturation subject to an additional constraint where the value used cannot be larger than (1.0 − S<sub>grw</sub> − S<sub>wirr</sub>) ∕ 1.5.
-   **S<sub>org</sub>:** This is modelled using the Al-Nuaimi equation where S<sub>org,imm</sub> = S<sub>orw</sub>. We also consider the additional constraint that the value used cannot be larger than 0.9 − S<sub>grw</sub> − S<sub>wirr</sub> (or zero if the constraint is less than zero).

Now we can also consider the relative permeability endpoints:

-   **k<sub>rocw</sub>:** Based on krow LET equation for oil-water drainage system at irreducible water saturation. This is equation [A4] in in Lomeland, Ebeltoft and Thomas (2005).<br>
    {% raw %}![k_{rocw}=\frac{\left(1-S_{wirr}\right)^{L_{o}^{w}}}{\left(1-S_{wirr}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{wirr}}^{T_{o}^{w}}}}](https://latex.codecogs.com/gif.latex?k_{rocw}=\frac{\left(1-S_{wirr}\right)^{L_{o}^{w}}}{\left(1-S_{wirr}\right)^{L_{o}^{w}}+E_{o}^{w}\cdot{{S_{wirr}}^{T_{o}^{w}}}}){% endraw %}
-   **k<sub>rwt</sub>:** Equal to k<sub>rwro</sub> for an oil-wet system or 1.0 otherwise.
-   **k<sub>rwro</sub>:** Based on k<sub>rwo</sub> (water-wet) LET equation for oil-water drainage system at effective saturation = 1.0 − S<sub>orw</sub> or zero if this is a negative result.<br>
    {% raw %}![k_{rwro}=\frac{\left(1-\frac{S_{orw}}{1-S_{wirr}}\right)^{L_{w}^{o}}}{\left(1-\frac{S_{orw}}{1-S_{wirr}}\right)^{L_{w}^{o}}+E_{w}^{o}\cdot{\left({\frac{S_{orw}}{1-S_{wirr}}}\right)^{T_{w}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rwro}=\frac{\left(1-\frac{S_{orw}}{1-S_{wirr}}\right)^{L_{w}^{o}}}{\left(1-\frac{S_{orw}}{1-S_{wirr}}\right)^{L_{w}^{o}}+E_{w}^{o}\cdot{\left({\frac{S_{orw}}{1-S_{wirr}}}\right)^{T_{w}^{o}}}}){% endraw %}
-   **k<sub>rgcw</sub>:** Based on k<sub>rgo</sub> LET equation for gas-oil drainage system at irreducible water saturation. This is equation [C4] in in Lomeland, Ebeltoft and Thomas (2005).<br>
    {% raw %}![k_{rgcw}=\frac{\left(1-S_{wirr}\right)^{L_{g}^{o}}}{\left(1-S_{wirr}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{wirr}}^{T_{g}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rgcw}=\frac{\left(1-S_{wirr}\right)^{L_{g}^{o}}}{\left(1-S_{wirr}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{{S_{wirr}}^{T_{g}^{o}}}}){% endraw %}
-   **k<sub>rgrl</sub>:** Based on k<sub>rgo</sub> LET equation for gas-oil drainage system at effective saturation = 1.0 – S<sub>org</sub>.<br>
    {% raw %}![k_{rgrl}=\frac{k_{rgcw}\left(1-\frac{S_{org}}{1-S_{wirr}}\right)^{L_{g}^{o}}}{\left(1-\frac{S_{org}}{1-S_{wirr}}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{\left({\frac{S_{org}}{1-S_{wirr}}}\right)^{T_{g}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rgrl}=\frac{k_{rgcw}\left(1-\frac{S_{org}}{1-S_{wirr}}\right)^{L_{g}^{o}}}{\left(1-\frac{S_{org}}{1-S_{wirr}}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{\left({\frac{S_{org}}{1-S_{wirr}}}\right)^{T_{g}^{o}}}}){% endraw %}
-   **k<sub>rorg</sub>:** Based on k<sub>rgo</sub> LET equation for gas-oil drainage system at normalised saturation = 1.0 − S<sub>grw</sub> or zero if S<sub>org</sub> + S<sub>grw</sub> + S<sub>wirr</sub> > 1.0.<br>
    {% raw %}![k_{rorg}=\frac{\left(1-\frac{S_{grw}}{1-S_{org}-S_{wirr}}\right)^{L_{g}^{o}}}{\left(1-\frac{S_{grw}}{1-S_{org}-S_{wirr}}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{\left({\frac{S_{grw}}{1-S_{org}-S_{wirr}}}\right)^{T_{g}^{o}}}}](https://latex.codecogs.com/gif.latex?k_{rorg}=\frac{\left(1-\frac{S_{grw}}{1-S_{org}-S_{wirr}}\right)^{L_{g}^{o}}}{\left(1-\frac{S_{grw}}{1-S_{org}-S_{wirr}}\right)^{L_{g}^{o}}+E_{g}^{o}\cdot{\left({\frac{S_{grw}}{1-S_{org}-S_{wirr}}}\right)^{T_{g}^{o}}}}){% endraw %}
	
These endpoints can be used to construct sets of oil-water and gas-oil two phase relative permeability curves for both drainage and imbibition. The sets of curves generated are consistent with each other. Gas-water curves are not considered separately as conventional simulator inputs only expect a set of oil-water and gas-oil curves. Whilst it is recognised that the gas-water relative permeability curves would be slightly different, the gas curves from the gas-oil system and the water curves from the oil-water system can be combined to create a proxy for a set of gas-water curves.

## Relative Permeability Curve Behaviour

### Porosity Variation

A consequence of how Pyrus models rock properties is that many parameters can be directly or indirectly linked to porosity. Ultimately the key inputs for Pyrus are the porosity and the nature of the rock fabric. From this are derived permeability, irreducible saturations, residual saturations etc. We should therefore expect a variation in the relative permeability curves to be observed.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-porosity.png" data-lightbox="image-3" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-porosity.png" alt="." />
	</a>
	<figcaption><strong>Figure 3: .</strong></figcaption>
</figure>

As porosity decreases, the irreducible water saturation increases. This affects the endpoint for the relative permeability to oil at critical water saturation (k<sub>rocw</sub>) and the relative permeability to gas at critical water saturation (k<sub>rgcw</sub>) which both reduce. Note that the LET correlations ensure that the relative permeability curves retain a smooth function and that there are no discontinuities evident as porosity is changed.

### Curve Shape

The shape factor β is the same shape factor used for generation of the capillary pressure curves and introduces a consistent basis for a relationship between the capillary pressure curve and the relative permeability curve. Note that the value of is β somewhat arbitrary, although values closer to 1 are consistent with tight, shaley sandstones and values close to 3 are consistent with vuggy carbonate. As with capillary pressure curves, a default value of 2 should perform adequately when generating relative permeability curves.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-shape-factor.png" data-lightbox="image-4" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-shape-factor.png" alt="." />
	</a>
	<figcaption><strong>Figure 4: .</strong></figcaption>
</figure>

The main difference between the curves as the shape factor increases is that the relative permeability at the point where water or gas flows preferentially to oil is decreased as the shape factor increases. In other words S<sub>ocr</sub> increases as the shape factor increases.

### Wettability

A different family of curves is obtained for oil-wet systems. Here oil is the wetting phase and there is less hysteresis apparent between the oil drainage and imbibition curves as a result, and more hysteresis apparent between the water drainage and imbibition curves. No adjustment has been made to the irreducible water saturation for an oil-wet system although this would more likely be similar to residual oil saturation in a water-wet system.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-wettability.png" data-lightbox="image-5" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-wettability.png" alt="." />
	</a>
	<figcaption><strong>Figure 5: .</strong></figcaption>
</figure>

### Miscibility

For the gas-oil system, the interfacial tension has an important influence on the relative permeability curves. Under miscible conditions, where interfacial tension decreases (especially lower than 0.1 dyne/cm<sup>2</sup>), the curves approach an “X” shape with endpoints close to effective saturations of 0.0 and 1.0. At higher IFTs the relative permeabilities exhibit more curvature and the critical oil saturation increases.

<figure>
	<a href="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-miscibility.png" data-lightbox="image-6" data-title=".">
		<img src="{{ site.url }}/images/Analysis/Permeability/rel-perm-versus-miscibility.png" alt="." />
	</a>
	<figcaption><strong>Figure 6: .</strong></figcaption>
</figure>

The behaviour of the generated curves is as expected. Note that the oil-water curves are not affected by gas-oil miscibility which is to be expected. As gas and oil become more miscible, the curves lose their curvature and both the critical oil saturation and residual oil saturation decrease. Note that for the water-wet rock, the gas imbibition curve saturation endpoint does not move. This is because the S<sub>grw</sub> (residual gas saturation after water invasion) is used and this value is unaffected by gas-oil miscibility. Since the two-phase relative permeability curve also captures the effect of irreducible water saturation, this is reasonable.

For an oil-wet system a similar approach to that used for S<sub>org</sub> can be employed to generate a value of S<sub>gro</sub> that also reduces below a critical IFT of 0.15 dyne/cm<sup>2</sup>. This eliminates the presence of trapped gas which is an immiscible fluid phenomenon. For an oil-wet reservoir, under miscible conditions the drainage and imbibition relative permeability curves form the idealised “X” shape.