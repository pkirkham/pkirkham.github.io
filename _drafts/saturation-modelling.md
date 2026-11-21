---
layout: article
title: Saturation Modelling
modified:
categories: analysis
excerpt: Explanation of proposed workflow for modelling saturations in 3D reservoir models.
tags: [pyrus_suite, static_model, dynamic_model, simulation, software, programming, simulation_deck, rock_properties, oil, gas, water, octave, resinsight, saturation_height, porosity, permeability, capillary_pressure, reservoir_engineering]
image:
  feature: feature-saturation-modelling-1024x256.jpg
  teaser: teaser-saturation-modelling-400x250.jpg
  thumb:
comments: false
mathjax: true
---

The oil industry involves many technical and non-technical disciplines. Often these disciplines end up working in silos, with little meaningful communication between the disciplines. By this I don't simply mean that within an individual company's asset team the technical team members are not talking to each other. In many organisations this does occur and team members are, on the whole, trying to work towards a common goal. No, the communication barrier is a level higher, within the language and approaches used by each discipline. To effectively cross these barriers requires an in-depth understanding of the technical approaches employed within each discipline, to reveal not only the rationale as to why one discipline's established techniques exist, but also to appreciate how the strengths and weaknesses of that discipline might influence the approaches used by a different discipline.

This can easily be dismissed as nice-sounding "motherhood and apple pie" and of little practical application. So let's consider a concrete situation that occurs in every single field development: the interface between the 'static' model and the 'dynamic' model. On the surface these are straightforward concepts. In the oil industry it common for different software and disciplines to be involved with the development and use of the static and dynamic models. Given that there is only one actual reservoir, it is reasonable to expect that the static and dynamic models are entirely consistent. This is not always the case.

## Static and Dynamic Models

Reservoir models are used to estimate the hydrocarbon resources that are held in-place within the pore space of a field's reservoir rock (the 'static' model), and the flow performance and recovery of hydrocarbons expected during production of fluid from the reservoir rock via wells (the 'dynamic' model).

### The Static Model

The static model deals with in-place hydrocarbons. It includes a geological realisation of the subsurface field, typically represented by a cellular grid framework of reservoir cells with properties related to the rock and the fluids contained in the pore space. Inputs to building the static model are typically obtained from:

 - **Geologists**: Provide the over-arching context for the field, including guidance on the rock properties and their statistical variation across the field, constraining interpretation to fit within conceptual structural models.
 - **Geophysicists**: Interpret seismic data to map the field's structure and horizons from which the grid framework is constructed. Seismic attribute analysis can also help to guide porosity distribution and fluid contact levels.
 - **Petrophysicists**: Interpret well logs to establish rock properties at known well locations within the static model. Static models must honour the 'ground truth' at these well locations, and properties are extrapolated from these points.

There are many other nuanced disciplines, and indeed many team members are able to work across several of these disciplines.

The contents of a static model (assuming black-oil formulation, rather than compositional) and the associated ECLIPSE keywords include:

 - **Gridded Cell Mesh**: The core framework for any reservoir model is the grid mesh. The model essentially works by calculating a classical material balance on each individual cell, via its connections to neighbouring cells or wells. Within the context of a static grid, the mesh framework allows more nuanced rock and fluid properties to be distributed spatially across a field, in comparison to the crude application of fieldwide average properties. Crucially the framework provides information concerning the cell bulk rock volume, and its spatial position, including depth (`DEPTH`).
 - **Porosity**: This is the primary rock property associated with each cell and defines the void space fraction within the rock cell that can be occupied by fluid according to the `PORO` keyword.
 - **Permeability**: Although it is not a static property, this rock property is often included in a static model as there is usually a modelled relationship between porosity and permeability. The permeability is provided either just as `PERMX` keyword for a single permeability, or with `PERMX`, `PERMY` and `PERMZ` keywords for directional permeability.
 - **Pressure**: The variation in fluid pressure is often modelled. If set, this would use the `PRESSURE` keyword. Usually the pressure is only needed for enumeration initialisation in the dynamic model, which is where the properties of each cell are set explicitly. This is not commonly used, as equilibrium initialisation can be used instead to calculate pressures in the dynamic model based on knowledge of a single pressure at a datum depth, and the depth of the gas-oil contact and oil-water contact.
 - **Saturation**: For each of the fluids in the field, the saturation profile for each of oil, gas and water within each cell is modelled. Typically just the initial water saturation `SWATINIT` is provided when using equilibrium initialisation. If enumeration initialisation used, then for a three-phase black-oil model, two of `SGAS`, `SOIL` and `SWAT` must be specified.
 - **Rock Type**: Geomodellers will sometimes identify different rock types or facies using an identifying number such as `SATNUM`.
 - **Interval**: Different intervals in the static model are often assigned a different number (`FIPNUM`) to allow for summary volumes in each region to be shown.

If we assume a rectilinear grid, with each cell volume given by its dimensions `DX`, `DY` and `DZ`, then the total hydrocarbon in-place (HCIP) can be calculated by summing the individual hydrocarbon volumes over all cells.

$$HCIP = \sum_{i=1}^{n} \frac{(DX_i \times DY_i \times DZ_i) \times \phi_i \times (1 - S_{w_i})}{FVF_i}$$

Here &phi; is the porosity, S<sub>w</sub> is the water saturation, and FVF represents the formation volume factor that represents the change in fluid volume between reservoir conditions and surface conditions. For example B<sub>oi</sub> is the variable used to represent the initial oil formation volume factor, being the oil and dissolved gas volume at reservoir conditions divided by oil volume at standard conditions. Typically B<sub>oi</sub> more than 1.0 RB/STB as oil contains dissolved gas which comes out of solution when going from reservoir pressure to surface conditions, leading to shrinkage of the liquid oil volume.

The equation for hydrocarbon-in-place is disarmingly simple. Essentially we calculate the gross rock volume for each cell, multiply by the porosity to get the pore volume, and then subtract the water saturation within that pore volume to get the hydrocarbon pore volume within each cell. Simple really. The challenge comes with determining the porosity and water saturation to use for each cell; both the porosity and water saturation affect the dynamic flow characteristics through their relationship to pressure, permeability and rock type.

### The Dynamic Model

The dynamic model takes the static model and simulates how the fluids flow within the reservoir in response to pressure changes. These pressure changes arise from the presence of well completions into the reservoir that apply a pressure drawdown at the wellbore so that fluids can flow into the well, or alternatively the well might be an injector, in which case there is an increase in pressure that causes fluids to flow into the reservoir. Inputs to building the dynamic model are typically obtained from:

 - **Reservoir Engineers**: Provide the over-arching context for the field, including guidance on the rock properties and their statistical variation across the field, constraining interpretation to fit within conceptual structural models.
 - **Petroleum Engineers**: Interpret seismic data to map the field's structure and horizons from which the grid framework is constructed. Seismic attribute analysis can also help to guide porosity distribution and fluid contact levels.
 - **Production Technologists**: Interpret well logs to establish rock properties at known well locations within the static model. Static models must honour the 'ground truth' at these well locations, and properties are extrapolated from these points.

### Disagreements Between Static and Dynamic Models

In an ideal world the static model is built by the geomodellers and the reservoir engineers simply run the exact same model through a reservoir simulator to determine the production rates and ultimate recovery factors. Prior to a field commencing production, this utopia probably does exist. There is very little reason for the reservoir engineer to question the validity of the static model. It is what it is.

The problems start once real production data is obtained. Suddenly there is a need to history match the dynamic model. After all, a model that can't even replicate the actual production results obtained is clearly a poor model.

### Reconciling a Universal Model

It should be apparent that any fundamental first principles based approach to static and dynamic modelling. In stating this, I hope I am not misconstrued as casting dispersions against saturation-height function (SHF) models, which are essentially correlations to approximate saturations based on the height above a free water level. These SHF models can be useful. However, it ought to be recognised that they are not generalist solutions, and that their simplistic (and non-dimensional) treatment of input variables obscures the contribution of any key rock parameters not explicity included in the SHF.

## The Hydrocarbon-Water Contact

### Different Types of Contact

a

<div style="width: 2000px; max-width: 100%; height: auto; margin: 20px auto 20px auto;">
	<video width="100%" height="auto" controls>
		<source src="https://www.dropbox.com/scl/fi/e2v76xqxbanjtccwgez71/Drainage-Imbibition-Animation.mp4?rlkey=733v16img00k0kj9r3jhdd8tv&raw=1" type="video/mp4" />
	</video>
</div>

### 