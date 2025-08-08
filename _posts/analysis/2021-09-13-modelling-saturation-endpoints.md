---
layout: article
title: Modelling Saturation Endpoints
modified:
categories: analysis
excerpt: Using a basic rock model to derive permeability from porosity and pore geometry for different lithologies.
tags: [static_modelling, rock_characterisation, rock_properties, irreducible_water_saturation, residual_gas_saturation, residual_oil_saturation, saturation_endpoint]
image:
  feature:
  teaser: teaser-saturation-endpoints-400x250.png
  thumb:
comments: true
mathjax: true
---

For a three-phase system, there are several endpoints that must be considered. These are the irreducible or residual saturations for a fluid where it is not possible to reduce the saturation below this value, and the critical saturations which are the saturation at which a fluid starts to flow.

For our static modelling approach, the objective is to create a framework whereby all reservoir engineering properties can be derived directly, or indirectly, from a rock model that is defined by geology. Therefore variations in the rock properties with respect to the rock character can be defined and our saturation endpoints will adjust such that they are physically consistent with the rock model.

## Irreducible Water Saturation

The irreducible water saturation (S<sub>wirr</sub>) and the critical water saturation (S<sub>wcr</sub>) are usually taken as the same value. The irreducible water saturation can be determined using the Holmes-Buckles relationship (Holmes et al., 2009). The Holmes-Buckles relationship is based on the classical Buckles equation where porosity times irreducible water saturation is a constant. The equation is modified to introduce a porosity exponent.

$$\phi^{Q}\times{S_{wirr}}=C$$

Where:

φ = Rock porosity, fraction<br>
Q = Porosity exponent, dimensionless<br>
S<sub>wirr</sub> = Irreducible water saturation, fraction<br>
C = Buckles constant, dimensionless

Holmes indicates that the value of Q can vary between 0.8 to 1.3, and that for many reservoirs a value of Q = 1 i.e., the original Buckles formula, is reasonable. A more significant variable in the equation is the constant, or the Buckles number. Holmes notes that ranges for the constant are:

-   **Sandstones:** 0.02 to 0.10
-   **Inter-granular carbonates:** 0.01 to 0.06
-   **Vuggy carbonates:** 0.005 to 0.06

Typically, the higher the constant, the smaller the average pore throat size within the rock matrix. In the absence of reliable data, a guide to the Buckles number can be determined from the rock type:

| Buckles Number | Sandstone | Carbonate |
|---|---|---|
| 0.12 | Very fine grain | Chalky |
| 0.06 | Fine grain | Cryptocrystalline |
| 0.03 | Medium grain | Inter-crystalline |
| 0.02 | Coarse grain | Sucrosic |
| 0.01 | Conglomerate | Fine Vuggy |
| 0.005 | Unconsolidated | Coarse Vuggy |
| 0.001 | Fractured | Fractured |

The values for C and Q should ideally be determined from petrophysical analysis. A plot of S<sub>wirr</sub> versus porosity will reveal to most appropriate values to use. In practice, particularly with high permeability carbonates that experience massive drilling fluid losses it can be difficult to reliably ascertain the true S<sub>wirr</sub> and porosity, in which case a method to calculate a proxy for C from other rock data is required.

The following relationships for sandstones and carbonates have been devised by considering the recommended Buckles numbers for different rock types and comparing these against typical cementation exponents for those rock types. These relationships are designed to capture the expected behaviour of different rock types, but it is cautioned that they have not been extensively, let alone rigorously, tested against large rock datasets.

$$C=0.0759m^{3}-0.2269m^{2}+0.2467m-0.0951$$ for carbonate m < 1.76<br>
$$C=0.3144m^{-3.324}$$ for carbonate m ≥ 1.76<br>
$$C=0.0243m^{-0.7}$$ for sandstone

To determine the parameter Q the default value = 1.0 (as per the original Buckles equation) is used for sandstones. For carbonates, Lucia’s recommended relationship between rock fabric number and water saturation (Jennings & Lucia, 2003) is used to establish a relationship between Q and C. As shown in Figure 1, the introduction of Q into the Holmes-Buckles equation, and use of the following equation to determine Q, allows an excellent match to Lucia’s published relationships.

$$Q=0.2018\ln{\left(\frac{1}{C}\right)}+0.442$$

<figure>
	<a href="{{ site.url }}/images/Analysis/Saturation Endpoints/holmes-buckles-swirr.png" data-lightbox="image-1" data-title="Improvement to irreducible water saturation prediction using Holmes-Buckles equation and comparison to Lucia carbonate trends.">
		<img src="{{ site.url }}/images/Analysis/Saturation Endpoints/holmes-buckles-swirr.png" alt="Improvement to irreducible water saturation prediction using Holmes-Buckles equation and comparison to Lucia carbonate trends." />
	</a>
	<figcaption><strong>Figure 1: Improvement to irreducible water saturation prediction using Holmes-Buckles equation and comparison to Lucia carbonate trends.</strong></figcaption>
</figure>

This is perhaps not too surprising as Lucia’s rock fabric number (RFN) equations have a similar form to the Holmes-Buckles equation. It is encouraging that the Holmes-Buckles approach and the Lucia RFN approach do not contradict each other, and it lends support to the assertion that irreducible water saturation can be fundamentally derived from knowledge of the rock porosity and the nature of its pore geometry.

In practice Lucia's relationships can lead to very high irreducible saturations. The consequence of this is that when creating a static grid using this relationship, the resultant SWCR value can be so high as to render the entire simulation unviable. A set of compromise relationships between the Buckles relationship and the Holmes-Buckles relationship that honours the Lucia relationship is therefore proposed:

$$Q=0.1489\ln{\left(\frac{1}{C}\right)}+0.4752$$

Using this approach allows C and Q to be determined from the cementation exponent alone, so irreducible water saturation is a function of porosity and cementation exponent which implies that this property is physically dependent on porosity and the pore geometry only. It should be expected that pore throat size has some influence on irreducible water saturation, and that the Buckle's 'C' constant is therefore a function of both cementation exponent and pore throat size. As implemented, this dependency has not been considered. Since our rock model can be used to derive the irreducible water saturation without any other inputs, the rock properties are the only inputs that need to be defined within a static model cell.

## Residual Gas Saturation

For our residual saturations, we are interested in the residual gas saturation (after water invasion) and the residual oil saturation (after both water invasion and gas invasion).

For the residual gas saturation we can use the method proposed by Holtz (2002) which modified the Land equation to produce a relationship that means the residual gas saturation is always equal to or less than the initial gas saturation. The method generates a family of curves depending on the rock quality, with higher porosity rock yielding lower residual gas saturation.

Gas trapping mechanisms are varied, and research suggests that high porosity rock can, under certain circumstances, have high residual or trapped gas as a result of ‘snap-off’ where large pore spaces which are connected by small pore throat sizes are bypassed by lower porosity but larger pore throat pathways. This is a phenomenon that is more likely observed with moldic porosity (separate non-touching vugs) and where the permeability related to the non-touching vugs falls below the average permeability for a given porosity. Therefore, there is preferential flow through the non-vug portion of the matrix.

For our purposes, we are more concerned with modelling a sensible behaviour that is in line with published data sources. The potential for higher residual gas saturation with higher porosity is not considered to simplify the model. Consequently we should expect that there is a relationship between increased residual gas saturation as initial gas saturation increases (as per Land). Similarly, we should expect that as porosity increases (and so does permeability), the pore spaces can be more readily swept by the wetting fluid and thus the residual gas saturation is lower for a given initial saturation.

This is the basis for the Holtz equation given by:

$$S_{gr}=\frac{1}{\left[\left(\frac{1}{S_{gtmax}}-1\right)+\left(\frac{1-S_{wirr}}{S_{gi}}\right)\right]}$$

Where:

S<sub>gr</sub> = Residual gas saturation, fraction<br>
S<sub>gtmax</sub> = Maximum trapped gas saturation, fraction<br>
S<sub>wirr</sub> = Irreducible water saturation, fraction<br>
S<sub>gi</sub> = Initial gas saturation, fraction

Holtz suggests a simple relationship between S<sub>gtmax</sub> and porosity for use in the residual gas saturation equation:

$$S_{gtmax}=-0.9696\phi+0.5473$$

Where:

φ = Rock porosity, fraction

Using these equations gives a series of Land’s curves for residual gas saturation versus initial gas saturation as shown below. The comparison datapoints are taken from a variety of datasets, including both published and unpublished data, and comprise more than 1,200 pairs of values. It can be seen that there is no clear distinction between sandstones and carbonates and that the modelled relationships fall wholly within the bounds of the datapoints.

<figure>
	<a href="{{ site.url }}/images/Analysis/Saturation Endpoints/sgr-vs-sgi.png" data-lightbox="image-2" data-title="Comparision of modelled residual gas saturation using Holtz equation at different porosities against measured datapoints.">
		<img src="{{ site.url }}/images/Analysis/Saturation Endpoints/sgr-vs-sgi.png" alt="Comparision of modelled residual gas saturation using Holtz equation at different porosities against measured datapoints." />
	</a>
	<figcaption><strong>Figure 2: Comparision of modelled residual gas saturation using Holtz equation at different porosities against measured datapoints.</strong></figcaption>
</figure>

The range of curves generated fits the spread of data observations for different rock types. The endpoints for these lines at 100% initial gas saturation, should also match the measured maximum trapped gas versus porosity. The modelled endpoints are plotted against published data points and exhibit a reasonable fit.

<figure>
	<a href="{{ site.url }}/images/Analysis/Saturation Endpoints/sgtmax-vs-phi.png" data-lightbox="image-3" data-title="Validation that variation of maximum trapped gas saturation with porosity implied by Holtz equation is consistent with published datasets.">
		<img src="{{ site.url }}/images/Analysis/Saturation Endpoints/sgtmax-vs-phi.png" alt="Validation that variation of maximum trapped gas saturation with porosity implied by Holtz equation is consistent with published datasets." />
	</a>
	<figcaption><strong>Figure 3: Validation that variation of maximum trapped gas saturation with porosity implied by Holtz equation is consistent with published datasets.</strong></figcaption>
</figure>

The residual gas saturation is a function of porosity and initial gas saturation. The latter is directly related to the irreducible water saturation through a saturation height function, and therefore residual gas saturation is a function of the three properties that define our simple rock model: porosity, cementation exponent and pore size. Therefore, changing our rock model will affect the residual gas saturation in a physically consistent manner.

## Residual Oil Saturation

The residual oil saturation will vary depending upon whether the displacing fluid is water or gas. The wettability of the rock must also be taken into consideration. This gives us two different residual oil saturations:

-   **S<sub>orw</sub>:** Residual oil saturation (after water invasion), fraction
-   **S<sub>org</sub>:** Residual oil saturation (after gas invasion), fraction

Let’s consider first S<sub>orw</sub>, the residual oil saturation after water invasion. Here the oil and the displacing fluid are immiscible. For a water-wet rock, whereby water adheres to the rock matrix, the residual oil saturation can be considered to be the same as the residual gas saturation. For an oil-wet rock, where the oil-water contact angle > 90 degrees) we might expect the behaviour of the residual oil to be similar to water as the wetting phase in a water-wet rock i.e. S<sub>orw</sub> = S<sub>wirr</sub>.

The situation for the residual oil saturation after gas invasion is different. In this scenario we must consider carefully the miscibility between the gas and oil phases. For a gas-condensate above the dewpoint pressure, the oil and gas phases can be considered miscible. Theoretically, at least, all the oil could therefore be displaced meaning the residual oil saturation is zero. On the other hand, at low pressure and temperature the oil and gas become immiscible and the fluids will behave more like the case of oil being displaced by water. In this case the wettability of the rock again becomes an important consideration.

Al-Nuaimi et al. (2018) published an interesting approach to modelling the change in S<sub>org</sub> in relation to the interfacial tension (IFT) between gas and oil using a [Michaelis Menten Kinetics](https://en.wikipedia.org/wiki/Michaelis%E2%80%93Menten_kinetics) model. As IFT decreases, the residual oil saturation to gas approaches zero.

$$S_{org}=\frac{S_{org,imm}\cdot\sigma}{\sigma_{crit}+\sigma}$$

Where:

S<sub>org,imm</sub> = Residual oil saturation to gas under immiscible conditions, fraction<br>
σ = Interfacial tension, dynes/cm<br>
σ<sub>crit</sub> = Critical interfacial tension = 0.15, dynes/cm

This equation produces a relationship whereby there is a sharp reduction in S<sub>org</sub> below the critical IFT, and a nearly constant S<sub>org</sub> above this point. Measuring σ<sub>crit</sub> directly is difficult as it requires finding the IFT at which S<sub>org</sub> = S<sub>org,imm</sub> / 2. The recommended approach in the paper is to estimate it using two S<sub>org</sub> measurements. One at immiscible conditions and one at partially miscible conditions. This is not possible with a modelled approach as there are no actual measurements from which to derive the critical interfacial tension. Therefore, for the purposes of our model we will simplify this even further and observe that we are simply looking for a reduction in S<sub>org</sub> under miscible conditions. We set σ<sub>crit</sub> to a fixed value of 0.15 and from this we can determine the miscible S<sub>org</sub>.

Unlike irreducible water saturation or residual gas saturation, the residual oil saturation is a function of fluid properties only and is independent of any rock properties. Therefore, changing the rock model by altering porosity, cementation exponent or pore size should have no effect on the residual oil saturation. This makes sense as the residual oil saturation is related to miscibility between hydrocarbon phases in the pore volume, and is not related to wettability of the pore space within the rock.

## References
 - Al-Nuaimi, L. Shahrokhi, O., Sohrabi, M., and Jahanbakhsh, A. 2018. A New Technique to Predict Relative Permeability for Two-phase Gas/oil System at Different Interfacial Tension. _SEG Global Meeting Abstracts_: 121-124. [https://doi.org/10.1190/RDP2018-39299276.1](https://doi.org/10.1190/RDP2018-39299276.1)
 - [Holmes, M., Holmes, A., and Holmes, D. 2009. Relationship between Porosity and Water Saturation: Methodology to Distinguish Mobile from Capillary Bound Water. Presented at AAPG Annual Convention, Denver, Colorado, 7-10 June.](https://www.searchanddiscovery.com/documents/2009/110108holmes/ndx_holmes.pdf)
 - Jennings, J. W. Jr. and Lucia, F. J. 2003. Predicting Permeability From Well Logs in Carbonates With a Link to Geology for Interwell Permeability Mapping. _SPE Res Eval & Eng_ **6** (04): 215–225. SPE-84942-PA. [https://doi.org/10.2118/84942-PA](https://doi.org/10.2118/84942-PA)
 - Holtz, M. H. 2002. Residual Gas Saturation to Aquifer Influx: A Calculation Method for 3-D Computer Reservoir Model Construction. Paper presented at at the SPE Gas Technology Symposium, Calgary, Alberta, Canada, 30 April–2 May. SPE-75502-MS. [https://doi.org/10.2118/75502-MS](https://doi.org/10.2118/75502-MS)