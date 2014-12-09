---
layout: article
title: Visual Core Analysis
modified:
categories: analysis
excerpt: Using ImageJ image processing software to determine visual porosity in carbonate cores.
tags: [pasca, core_analysis, research, image_processing]
image:
  feature: 
  teaser: teaser-visual-core-analysis-400x250.jpg
  thumb:
comments: true
---

The Pasca A field in the Gulf of Papua was first discovered in 1968. This is a carbonate pinnacle reef structure containing a liquids-rich gas-condensate. During drilling of an appraisal well during 1983 there was a loss of well control event when pulling out of hole prior to logging, and until recently it was believed (mistakenly) that the field no longer contained any recoverable hydrocarbons as a result.

Twinza Oil believes that the field only suffered a minor loss of hydrocarbons, and that there remains a substantial undeveloped resource. The question of course is exactly how large is that resource. One of the uncertainties associated with the resource estimate is the average field porosity. Given that there are only logs available from two wells drilled in 1968 and 1969, plus core from the Pasca A-3 well drilled in 1983, there is not a huge amount of data available. Furthermore the geological model would indicate that the two wells which were logged, were drilled on the flanks of the reef and thus could be associated with a lower porosity than that in the centre of the reef. However the porosity measurements from the core plugs in Pasca A-3 suggest the very opposite; the porosity is lower in the centre of the reef.

## The Core Analysis Challenge

Best practice for any evaluation effort is to start with the raw data itself, and understand the limitations of how it was acquired and interpreted. In the case of Pasca A-3 this was not straightforward as the core recovery was not high to begin with, and the core has been stored in a warehouse in PNG for the last three decades.

Fortunately it was possible to locate most of the core, and thus there is hope that further analysis may prove useful. Given that there is a limited amount of core, the preference is to use non-destructive analytical techniques, such as MRI or CT scanning to build up a picture of the pore space within the core. As a pre-cursor to these activities high resolution photographs of the core were taken, to investigate whether the more expensive scanning techniques would yield any benefits?

An example section from one of the core photographs is shown in Figure 1. This illustrates that there is visible macro porosity in the form of vugs, and this immediately suggests that the reservoir could have a significant porosity. These vugs are easily identified and are associated with flow of fluids through the reef post-deposition, which have contributed to dissolution porosity.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide1.png" data-lightbox="image-1" data-title="Evidence of vugs on core">
		<img src="{{ site.url }}/images/visual-core-analysis-slide1.png" alt="Evidence of vugs on core"/>
	</a>
	<figcaption><strong>Figure 1: Evidence of vugs on core.</strong></figcaption>
</figure>

Another aspect of porosity development in carbonates is associated with formation of dolomite. Dolomite forms as a result of mineral alteration whereby calcium ions in calcite are replaced by magnesium ions. This is evidence of freshwater incursion into the reef, mixing with saline formation water. As a result of the dolomitization porosity is generated because of a volume reduction in the carbonate matrix.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide2.png" data-lightbox="image-2" data-title="Evidence of dolomitic porosity on core">
		<img src="{{ site.url }}/images/visual-core-analysis-slide2.png" alt="Evidence of dolomitic porosity on core"/>
	</a>
	<figcaption><strong>Figure 2: Evidence of dolomitic porosity on core.</strong></figcaption>
</figure>

Analogue carbonate reservoirs with vuggy porosity can easily develop effective porosity of 20 percent or higher. So the apparent history concerning the porosity development is contrary to the 11 percent porosity determined from log interpretation. Is it possible to justify the use of a higher porosity upside in the Pasca A field based on the core? To answer this question it is first necessary to measure the visible porosity from the core photograph.

It should be noted that it is also expected that microporosity will be present in the carbonate matrix, but it is not possible to measure this through a simple visual technique. Therefore any visual porosity estimate technique will be an underestimate of the true porosity.

## Using ImageJ To Calculate Porosity

The [ImageJ open source image processing program](http://imagej.net/Welcome) can be used to manipulate images and identify features. In particular it permits thresholding of an image, whereby an image can be converted to a binary image, by setting specific conditions for characterisation of the image as black or white. Statistics can be calculated for any binary image, at both a pixel level, and also through identification of discrete features. This should allow individual pore spaces to be captured and assessed.

In addition to identification and measurement of porosity, it should be possible to discern between different facies. In particular there are three different categories of carbonate facies that can be distinguished in the core photograph. These are:

1. Biogenically altered facies which have a lighter hue, and are likely to be associated with algal matter.
2. Dolomotized carbonate which has a darker hue.
3. Background facies.

The goal is to use ImageJ to separate areas on the core photograph that represent porosity, biogenic facies, dolomite facies and carbonate matrix.

### Extracting Features From Core Photographs

Generally the pore space in the core photograph will be darker. This is because light is reflected off the surface of the core, but there is less light that can enter the pore space directly and thus the pores tend to contain shadow. As a result we can convert the red, green and blue channel (RGB) to hue, saturation and brightness space (HSB), and apply a threshold filter based on brightness.

1. Adjust the image levels using Image/Adjust/Window/Level... and select 'Auto' then 'Apply'
2. Sharpen the image using Process/Sharpen
3. Apply a brightness threshold using Image/Adjust/Color Threshold. Choose 'Thresholding method' Yen, 'Threshold color' B&W, 'Color space' HSB.
4. Using a red threshold colour initially and switching between the original and filtered image can help tweak the cut-off points used in the thresholding
5. Convert to a binary image using Process/Binary/Make Binary
6. Remove artefacts from porosity mask using Process/Binary/Dilate followed by Process/Binary/Fill Holes

The results are shown in Figure 3.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide3.png" data-lightbox="image-3" data-title="Porosity identified from brightness threshold">
		<img src="{{ site.url }}/images/visual-core-analysis-slide3.png" alt="Porosity identified from brightness threshold"/>
	</a>
	<figcaption><strong>Figure 3: Porosity identified from brightness threshold.</strong></figcaption>
</figure>

A similar strategy can be employed to distinguish the difference facies in the non-porosity areas of the image. In this instance the very lighter and darker areas of the image have been distinguished using the same threshold technique, but by adjusting the hue and saturation filters. Care has to be taken to avoid falsely identifying areas within the already identified porosity or identifying any pixel as belonging to two facies groups. This can be achieved through subtraction of one image from another using Process/Image Calculator... The results are shown in Figure 4.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide4.png" data-lightbox="image-4" data-title="Facies identified from saturation threshold">
		<img src="{{ site.url }}/images/visual-core-analysis-slide4.png" alt="Facies identified from saturation threshold"/>
	</a>
	<figcaption><strong>Figure 4: Facies identified from saturation threshold.</strong></figcaption>
</figure>

Here we have categorised our original image into four different groups. The red areas represent porosity, with contribution from both the vugs and smaller pores. The green areas are the very light carbonate rock fabric which could be associated with biological matter. The yellow areas are the darker carbonate rock fabric which could be associated with dolotomisation and the blue area is simply what is left over. This is the base carbonate matrix.

The image reveals some interesting features of the reservoir. We can see that areas of dolomitisation tend to lie along linear trends which could represent fluid flow through the reservoir. These fluids have chemically altered the base rock matrix to cause the dolomotisation. Furthermore the fluid flow may have created vugs, and there are more vugs close to the potential fluid flow pathways. Small pore spaces are more prevalent in areas of dolotomisation and away from biological matter. This might be expected because the dolotomisation contributes to the formation of pore space as a result of fluid flow through the reservoir, but the biological matter restricts flow of fluids through the carbonate, so there is less dolomotisation in those areas. Vugs presents in areas of biological matter could be created through larger organisms that have decayed, and are thus different to the vugs in the dolotomised regions which might have resulted from fluid flow.

Whilst the image analysis is a relatively straightfoward technique, it does help us to understand that there is very likely to have been fluid flow in the reservoir, and that therefore there is very likely to be good permeability in the reservoir. We can also recognise using our image processing the two main types of porosity that are revealed through visual examination of the core.

### Porosity Calculation From Binary Mask

Calculation of porosity from a binary image is relatively simple using ImageJ. ImageJ will measure various image statistics when the Analyze/Measure command is run. The statistics to measure can be set from Analyze/Set Measurements... command. For the calculation of porosity we must ensure that the 'Area' and 'Area fraction' options have been selected.

Once the measurements have been selected calculation of porosity is simple:

1. Select the binary image where porosity is identified as black pixels and matrix as white pixels
2. Run command Analyze/Measure
3. Porosity is shown in the '%Area' column of the Results window that is produced

The calculated porosity using the porosity mask generated from our core photograph is shown in Figure 5. This indicates that porosity of the image shown is 10.4 percent.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide5.png" data-lightbox="image-5" data-title="Calculated porosity from binary porosity layer">
		<img src="{{ site.url }}/images/visual-core-analysis-slide5.png" alt="Calculated porosity from binary porosity layer"/>
	</a>
	<figcaption><strong>Figure 5: Calculated porosity from binary porosity layer.</strong></figcaption>
</figure>

Note that this whilst this technique can quickly produce an indicative measurement for the porosity, it does have some shortcomings:

* Not all porosity is captured by the threshold technique. In particular larger vugs do not produce a dramatic brightness contrast across the entire pore as light can more easily penetrate into the pore space, whereas smaller pores can be easily identified. Similarly areas of darker rock facies can be mistaken for porosity. A visual comparison between the core photograph and the porosity mask suggests that the porosity measured using this technique is likely to be a slight underestimate.
* Only porosity that can be visually identified will be measured using this technique. Thus any microporosity will not be included in the measured porosity.

It may be necessary when calculating porosity to exclude certain parts of the image. This can be achieved by measuring the statistics within the area to be excluded, and then adjusting the statistics for the whole image to account for the difference. This is a straightforward calculation.

1. Draw an area on the binary image that is to be excluded
2. Run command Analyze/Measure
3. Results window should now show an additional row which includes statistics for the excluded area only
4. Adjust porosity calculation using the equation `((img_area * img_area_pct) - (excl_area * excl_area_pct)) / (img_area - excl_area)`

### Understanding Porosity Distribution

The core photograph shows a visual porosity measurement of 10.4 percent which agrees quite well with the log measured porosity of 11 percent. If the reservoir was relative homogeneous, then we might conclude that there is no apparent difference in porosity revealed by the core at the centre of the reef versus that interpreted from logs at the flanks of the reef.

The reef is not homogeneous, and other sections of core exhibit more prevalent vugginess. An example of this is shown in Figure 6. This core photograph has been analysed using the excluded area technique because part of the core was removed to take a core plug. Records indicate that the laboratory measured porosity for the core plug was just 7.8 percent; even lower than the 11 percent interpreted from the logs. Reliance on this core porosity without examination of the core might lead to a belief that the reef does not develop good porosity in the centre. Yet it can clearly be seen that there are extensive large vugs in the core.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide6.png" data-lightbox="image-6" data-title="Comparison of visual porosity estimate against measured core plug porosity">
		<img src="{{ site.url }}/images/visual-core-analysis-slide6.png" alt="Comparison of visual porosity estimate against measured core plug porosity"/>
	</a>
	<figcaption><strong>Figure 6: Comparison of visual porosity estimate against measured core plug porosity.</strong></figcaption>
</figure>

The visual porosity estimate is 14.6 percent. This is nearly twice as high as the core measured porosity. Does this mean that the core measured porosity or the visual porosity estimate is wrong? Not at all. Any core plug is very likely to be taken from a section of the core that is competent and can from which porosity can easily be measured using laboratory techniques. The intent would therefore be to avoid taking a core plug through the vuggiest part of the core. As such the core plug is only measuring part of the porosity.

In order to understand this more clearly, we can look at the porosity distribution. By taking our binary mask for porosity, we can filter the image based on the areas of discrete features. In Figure 7 we have assigned features into five different area bins: 0-49 pixels, 50-99 pixels, 100-149 pixels, 150-199 pixels and 200+ pixels.

<figure>
	<a href="{{ site.url }}/images/visual-core-analysis-slide7.png" data-lightbox="image-7" data-title="Porosity distribution from pore size bins">
		<img src="{{ site.url }}/images/visual-core-analysis-slide7.png" alt="Porosity distribution from pore size bins"/>
	</a>
	<figcaption><strong>Figure 7: Porosity distribution from pore size bins.</strong></figcaption>
</figure>

This porosity calculated for each bin image helps to understand the nature of the porosity distribution in the reef.

* There is more porosity contribution from very small pores than from medium sized pores. The overall porosity from small to medium size pores is 5.6 percent, which is in line with the core plug measured porosity of 7.8 percent.
* Over half of the porosity is associated with larger pores and vugs.

This reveals that porosity in the Pasca A reef is not a straightforward property to characterise, but there is evidence that the average porosity might be higher than that calculated from logs on the flanks of the reef. It should be noted that the poor core recovery, drilling breaks and loss of circulation during drilling are consistent with a porosity model that also includes some porosity contribution from very large dissolution features.

## Conclusions

A visual technique has been used to estimate porosity in the Pasca A reef. The technique combines digital photographs of the core surface with image processing algorithms to calculate the porosity. The technique is also able to distinguish between different types of porosity, and the relative contribution from each.

The technique is non-destructive, cheap and fast. By examining the original core and using this simple first principles approach, a case can be made to support the use of more sophisticated techniques to image the core porosity in three dimensions. Doing so will help to understand the geological processes that were present post-deposition, and help understand the upside potential for porosity in the field.