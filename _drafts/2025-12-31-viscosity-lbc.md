---
layout: article
title: Lohrenz-Bray-Clark Viscosity
modified:
categories: analysis
excerpt: Detailed implementation for the Pedersen corresponding states principle (CSP) viscosity model, including Java code snippets.
tags: [pyrus_suite, dynamic_model, simulation, software, programming, fluid_properties, oil, gas, reservoir_engineering, viscosity, residual_viscosity]
image:
  feature: feature-viscosity-lbc-1024x256.jpg
  teaser: teaser-viscosity-lbc-400x250.jpg
  thumb:
comments: false
mathjax: true
---

Lohrenz, Bray and Clark (1964) outlined an approach to calculating visocity of any hydrocarbon, whether gas or oil. The method first calculates a low-press pure component gas viscosity for all components which incorporates any effects from temperature. The component viscosities are combined into a low-pressure mixture gas viscosity using a mixing rule. Depending on whether the fluid density is gaseous or liquid, either a corresponding states approach is used for gas, or a residual viscosity approach is used for liquid.

It is interesting to note that the oil and gas industry uses the Lohrenz-Bray-Clark (LBC) method extensively, even when other more accurate methods have been developed. To add to the confusion, the LBC approach describes a workflow to determine viscosity for any hydrocarbon fluid. It builds on and incorporates existing equations, and does not introduce any new equations of its own. In particular, the residual viscosity approach to determine viscosity of liquids uses the approach of Jossi, Stiel and Thodos (1962). It was noted that this could be applied to gases as well as liquids, but the absence of a suitably accurate gas density prediction method led to their recommendation to use the older graphical corresponding states method (albeit with computer assisted lookup table interpolation).

When it is stated that the LBC co-efficients need to be tuned to match a viscosity model, what is really being stated is that the Jossi, Stiel and Thodos co-efficients need to be tuned. This is expected, as the original paper provided different co-efficients for different types of fluid.

In other words, any criticisms and shortcomings perceived of the LBC method are most likely issues related to the underlying viscosity equations being used. Lohrenz et al observe that since the calculation procedure is a sequence of steps, it is possible to update or change any step. By substituting new and improved methods into the steps, the workflow remains relevant, and its overall accuracy is improved.

## Lohrenz-Bray-Clark Method

The LBC approach is based on the residual viscosity concept and the theory of corresponding states. A series of steps is followed:

 1. **Break-up of the heptane-plus fraction.**

    The C7+ fraction is ideally split into smaller components from C7 through to C40. The LBC paper notes that it is assumed that the split components are normal paraffins for convenience, and their procedure did not account for paraffinic or aromatic character in the split fractions. This shortcoming was outweighed by the observation that including even small amounts of very heavy fractions avoids large discrepancies in the results.

 2. **Calculate low-pressure pure-component gas viscosities at the temperature of interest.**

    The low-pressure pure component gas viscosity is calculated for all components in the mixture using the Stiel and Thodos correlation.

    For _T<sub>ri</sub>_ < 1.5:

    $$\mu_i^{*}\xi_i=34\times10^{-5}T_{ri}^{0.94}$$

    For _T<sub>ri</sub>_ > 1.5:

    $$\mu_i^*\xi_i=17.78\times10^{-5}\left(4.58T_{ri}-1.67\right)^{5/8}$$

    With:

    $$T_{ri}=\frac{T}{T_{ci}}$$

    $$\xi_i=\frac{T_{ci}^{1/6}}{M_{wi}^{1/2}p_{ci}^{2/3}}$$

    Where:

     - _T<sub>ri</sub>_ = component reduced density
     - _&xi;<sub>i</sub>_ = component viscosity-reducing parameter &#8776; 1 / _&mu;<sub>ci</sub>_ (cP<sup>-1</sup>)
     - _T<sub>ci</sub>_ = component critical temperature (K)
     - _p<sub>ci</sub>_ = component critical pressure (atm)
     - _M<sub>wi</sub>_ = component molecular weight (g/mol)

 3. **Calculate low-pressure mixture gas viscosity.**

	Herning and Zipperer (1936) is used as a mixing rule to combine the pure component low-pressure viscosities.

    $$\mu^{*}=\frac{\sum_{i=1}^{n}\mu_i^{*}z_i\sqrt{M_i}}{\sum_{i=1}^{n}z_i\sqrt{M_i}}$$

    Other mixing rules were tested, and it was found that Herning and Zipperer had the best performance when tested against the available dataset.

 4. **Determine whether fluid is GAS or LIQUID.**

    No guidance is provided in the LBC paper as to how this should be determined.

If GAS fluid:

 1. **Calculate reduced temperature and pressure of the gas mixture.**

    Average pseudocritical values can be calculated using simple mixing rules for the critical properties.

    $$T_r=\frac{T}{\sum_{i=1}^{n}\left(z_iT_{ci}\right)}$$

    $$p_r=\frac{p}{\sum_{i=1}^{n}\left(z_ip_{ci}\right)}$$

 2. **Calculate viscosity of the gas mixture at the temperature and pressure of interest.**

    The chart of Baron, Roof and Wells is used which plots the viscosity ratio _&mu;_ / _&mu;<sup>*</sup>_ similar to Comings et al (1944). Baron, Roof and Wells contains data for several pure components at different temperatures and pressures which allows interpolation of the points to be computed. Essentially this is a lookup table approach.

    $$\frac{\mu}{\mu^{*}}=f(T_r, P_r)$$

Else if LIQUID fluid:

 1. **Calculate the density of the liquid mixture at the temperature and pressure of interest.**

    The LBC paper simply states that "the Alani and Kennedy method is used", but is light details. The equation is used without modification with the exception of nonhydrocarbon components, where new constants are used for hydrogen sulphide, nitrogen and carbon dioxide so as to improve the liquid density predictions for reservoir mixtures containing these components.

 2. **Calculate reduced density of the liquid mixture.**

    Reduced density is given by the density _&rho;_ divided by the critical density _&rho;<sub>c</sub>_.

    $$\rho_r=\frac{\rho}{\rho_c}$$

    The pseudocritical density is calculated from the pseudocritical volume.

    $$\rho_c=\frac{M_w}{V_c}$$

    With:

    $$V_c=\sum_{\substack{i=1\\i\neq C_{7+}\\}}^{n}(z_iV_{ci})+z_{C_{7+}}V_{cC_{7+}}$$

    $$V_{cC_{7+}}=2.573+0.015122M_{C_{7+}}-27.6561G_{C_{7+}}+0.070615M_{C_{7+}}G_{C_{7+}}$$

    Where:

     - _V<sub>c</sub>_ = critical volume
     - _V<sub>cC<sub>7+</sub></sub>_ = critical volume of heptane-plus fraction
     - $$M_{C_{7+}}$$ = molecular weight of heptane-plus fraction (lbm/lbm-mol)
     - $$G_{C_{7+}}$$ = specific gravity of heptane-plus fraction (water = 1.0)

 3. **Calculate viscosity of the liquid mixture.**

    The relationship developed by Jossi et al (1962) is used to determine the liquid mixture viscosity _&mu;_.

    $$\left[\left({\mu-\mu^{*}}\right)\xi+10^{-4}\right]^{1/4}=0.1023+0.023364\rho_r+0.058533\rho_r^2-0.40758\rho_r^3+0.0093324\rho_r^4$$

    With:

    $$\xi=\frac{[\sum_{i=1}^{n}(z_iT_{ci})]^{1/6}}{[\sum_{i=1}^{n}(z_iM_{i})]^{1/2}[\sum_{i=1}^{n}(z_iP_{ci})]^{2/3}}$$

    Where:

     - _&mu;<sup>*</sup>_ = low-pressure mixture gas viscosity (cP)
     - _&xi;_ = mixture viscosity-reducing parameter &#8776; 1 / _&mu;<sub>c</sub>_ (cP<sup>-1</sup>)

Lohrenz, Bray and Clark note that because the calculation procedure is a sequence of steps, it is possible to update or change any step. They note that since they devised the procedure, new techniques had emerged which could both simplify the calculations and/or improve the accuracy. It is also noted that it should be possible to make gas viscosity predictions using an identical procedure to that applied for liquid viscosities, but the lack of a reliable gas density prediction method (at the time the LBC paper was written) prevented them from pursuing this route.

### LBC Considerations

LBC is frequently used in compositional simulation software because the algorithm is inherently faster than the corresponding states approach. Pedersen notes that "as a predictive model, LBC will usually provide results of low quality" which means that often the LBC model is tuned to match laboratory measurements. This is done by varying either the _a<sub>1</sub>_ through _a<sub>5</sub>_ coefficients in the Jossi et al equation that relates residual viscosity to reduced density, or through changing the critical volume for the C7+ component. This is reasonable as it was observed by Jossi et al that the coefficients are not constants, and appear to depend on the critical compressibility. Pedersen also notes that "the fundamental assumption of a unique relationship between reduced density and viscosity, especially for heavy oil mixtures, is a questionable assumption".

A shortcoming of the LBC method is that, as noted by Whitson, it [does not predict monotonically increasing viscosity for higher carbon numbers](https://manual.pvt.whitson.com/modules/fluid_model_development/viscosity_modeling/). Given that the LBC model is in almost ubiquitous use throughout the oil and gas industry, this was a somewhat startling revelation. I mean, really? The expected behaviour is that as the hydrocarbons get bigger in size, the viscosity should increase. A validation of the assertion can be seen in Figure 1 where liquid viscosity predictions using Pyrus' implementation of LBC have been plotted on top of Whitson's results. This confirms that there is a non-monotonic trend. Whitson notes that this defective behaviour can be overcome through use of a different method to estimate the component viscosities, such as the group contribution methods outlined below.

<figure>
	<a href="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure1.png" data-lightbox="image-2" data-title="Single carbon number liquid viscosity modelled using Lohrenz-Bray-Clark methodology fails to exhibit monotonically increasing behaviour.">
		<img src="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure1.png" alt="Single carbon number liquid viscosity modelled using Lohrenz-Bray-Clark methodology fails to exhibit monotonically increasing behaviour."/>
	</a>
	<figcaption><strong>Figure 2: Single carbon number liquid viscosity modelled using Lohrenz-Bray-Clark methodology fails to exhibit monotonically increasing behaviour.</strong></figcaption>
</figure>

Another challenge with the LBC method is that it has been noted that the predicted viscosity is sensitive to the density calculation. Accurate density predictions from the equation of state being used are essential for this method to be applied.

## References

 - [Comings, E. W., Mayland, B. J., and Egly, R. S. 1944. The Viscosity of Gases at High Pressures. _University of Illinois Engineering Experiment Station Bulletin No. 354_.](http://hdl.handle.net/2142/4478)
 - Herning, F. and Zipperer, L. (1936). Beitrag zur Berechnung der Zähigkeit Technischer Gasgemische aus den Zähigkeitswerten der Einzelbestandteile (Calculation of the Viscosity of Technical Gas Mixtures from Viscosity of the individual Gases), _Gas und Wasserfach_ **79**: 49–54, 69-73.
 - Jossi, J. A., Stiel, L. I., and Thodos, G. 1962. The Viscosity of Pure Substances in the Dense Gaseous and Liquid Phases. _AIChE Journal_ **8**: 59-63. [https://doi.org/10.1002/aic.690080116](https://doi.org/10.1002/aic.690080116).
 - Lohrenz, J., Bray, B.G., and Clark, C.R. 1964. Calculating Viscosities of Reservoir Fluids From Their Compositions. _J Pet Technol_ **16** (10): 1171–1176. SPE-915-PA. [https://doi.org/10.2118/915-PA](https://doi.org/10.2118/915-PA).