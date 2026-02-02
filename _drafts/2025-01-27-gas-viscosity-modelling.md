---
layout: article
title: Gas Viscosity Modelling
modified:
categories: analysis
excerpt: Approach to gas viscosity prediction used in Pyrus based on group contribution methods.
tags: [pyrus_suite, dynamic_model, simulation, software, programming, simulation_deck, fluid_properties, gas, reservoir_engineering, viscosity, corresponding_states, residual_viscosity, group_contribution]
image:
  feature: feature-gas-viscosity-modelling-1024x256.jpg
  teaser: teaser-gas-viscosity-modelling-400x250.jpg
  linkedin: Analysis/viscosity-modelling/viscosity-figure5.png
  thumb:
comments: false
mathjax: true
---

Viscosity governs how easily a fluid will flow and can be conceptualised as the internal fluid friction which opposes any dynamic changes to fluid flow. This makes it a very important parameter in dynamic simulation of reservoir models. Great effort has gone into understanding how viscosity can be altered to improve oil recovery, for example with thermal methods such as steam flooding that help reduce the viscosity of heavy oil and thus aid recovery.

Dynamic viscosity _&mu;_ is the absolute measure of how easily a fluid will flow. It has a value of about 1.0 centipoise (cP) for water at 20 &deg;C which makes the unit convenient to interpret. Sometimes kinematic viscosity is reported, which is simply the viscosity divided by the density (_&mu;_ / _&rho;_) and is typically shown with units of stokes (St). Note that one centistoke (cSt) is 1 cP divided by 1,000 kg/m<sup>3</sup> which is approximately the density of water. Thus for water, the dynamic viscosity is close to 1 cP and the kinematic viscosity is close to 1 cSt.

Viscosity can be [measured in a laboratory using a viscometer](https://en.wikipedia.org/wiki/Viscometer) or estimated using correlations or other similar analytical methods. Note that viscosity reported in oilfield laboratory reports may not necessarily be measured as it is common to report the viscosity as calculated using a correlation.

In Pyrus, where fluid composition or type may be estimated, but actual measurements do not exist, it is necessary to implement a method to predict viscosity as accurately as possible. This assists with generation of reliable black oil tables for use in reservoir simulators. A variety of methods have been compared so as to determine the most robust and predictive approaches.

## Modelling Viscosity

Whilst there are some methods for viscosity calculation that apply to both gases and liquids, in general the treatment of gases and liquids is different. In this article we cover the methods applicable to gas viscosity.

### Viscosity Theory

As viscosity is the measure of internal fluid friction, it follows that the application of a shear stress on part of a fluid will impart a localised velocity to that part of the fluid. We can visualise a fluid as being made up of very small elements. Elements adjacent to the moving part of the fluid will have a frictional drag imparted onto them due to collisions between the molecules, causing them to move with a velocity that is lower than the element imparting the frictional drag. As the distance from the element onto which the original shear stress is imparted increases, the velocity decreases, leading to a velocity gradient. In addition to the elastic collisions between molecules, the inter-molecular forces (either attractive or repulsive) can also be considered.

An illustration of how a shear force applied to a finite volume containing randomly moving molecules of gas sets up a velocity gradient via the interaction between molecules in layers of constant velocity is shown in the figure below.

<figure>
	<a href="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" data-lightbox="image-1" data-title=".">
		<img src="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" alt="."/>
	</a>
	<figcaption><strong>Figure 1: .</strong></figcaption>
</figure>

Viscosity is defined as the local shear stress per unit area divided by the velocity gradient. Thus, for a given imposed shear stress, the lower the associated velocity gradient, the higher the viscosity. Viscosity has units of mass / (length &times; time) with the commonly used unit 1 Poise being equal to 1 g / (cm &sdot; s) or 0.1 Pa &sdot; s. Thus 100 cP = 1 mPa &sdot; s.

An interesting aspect of viscosity is that it can only be measured if the fluid is moving, and is thus referred to as a non-equilibrium property. Despite this, viscosity is a function of the thermodynamic state of the fluid, and varies with temperature and pressure.

<div class="notice-warning">In this article we only deal with Newtonian fluids. These are fluids where the viscosity is independent of the magnitude of shear stress or velocity gradient e.g., the viscosity does not change. Polymers, including drilling muds, require different approaches.</div>

## Viscosity Models

Generally there are several types of viscosity model.

 1. **Correlation**: Correlations have been devised from large datasets to predict viscosity for gases and liquids against common field measurements. These approaches require that high level fluid parameters such as the gravity and saturation pressures are known. For well behaved typical oil and gas fluids, these approaches can be considered robust.
 2. **Theoretical**: Theoretical models combine effects of kinetic theory and intermolecular forces. Most approaches are based on the equations derived by Chapman and Enskog. Since this approach is based on the behaviour of interactions between colliding molecules, and the attractions (or repulsions) that exist between them, it is only applicable to modelling of gas viscosity.
 3. **Corresponding States**: Based on the principle that fluids will exhibit similar thermodynamic behaviour at the same reduced temperature and reduced pressure, it is possible to create a model for viscosity based on a known fluid and extend this to other components or mixtures. This is essentially similar to the [theory of corresponding states](https://en.wikipedia.org/wiki/Theorem_of_corresponding_states) that underpins the well-known Standing and Katz z-factor chart which uses the reduced pressure and reduced temperature to determine the z-factor. In other words, for any gas at the same conditions relative to its critical point, will exhibit similar deviation from ideal gas behaviour.
 4. **Residual Viscosity**: A relationship between the density of a fluid and its viscosity exists. The difference between the viscosity and the viscosity of the fluid at atmospheric conditions is referred to as the residual viscosity. It can be shown that values of residual viscosity plotted against density fall onto a curve, and that this appears to be independent of temperature.
 5. **Group Contribution Techniques**: Models based on group contribution methods can support predictive viscosity modelling if the composition of the fluid is known. These are typically used for low-temperature liquid viscosity approaches where corresponding states methods are less accurate. In contrast, group contribution methods take into consideration the effects of the chemical structure on viscosity.

In the oil and gas industry, both simple correlations and the Lohrenz-Bray-Clark (LBC) method are the most widely used viscosity models. This is intruiging given the importance of viscosity to Darcy's equation that governs dynamic flow. Would a more fundamental approach not be better?

The reason appears to be grounded in the use of compositional simulation models. With compositional simulation, the viscosity must be recalculated for the composition in every cell at every timestep. This can lead to millions of calculations, and thus a fast method to obtain viscosity is desirable. Whilst correlations or the LBC model may not be reliably predictive, it is possible to tune then to available laboratory measurements. This allows a fast and accurate model to be implemented where laboratory data are available to tune the model.

In contrast, the Pyrus approach attempts to be as predictive as possible with limited available data. Thus a reliable predictive method is needed to determine viscosity. A comparison between different models is discussed as a means to determine the most appropriate methods to utilise.

### Correlation Models

Correlation models are a straightforward empirical approach used to predict properties from a range of input variables. Usually the input variables are known to have either a physical basis for the relationship or can be shown to be statistically significant drivers of the predicted property. This is not always the case. At the time of writing, there has been an increased interest in so-called "AI" models based on training neural nets with a dataset. When distilled down to their fundamental core methodology, it is revealed that these are no more than black-box empirical correlation methods. Whether the input variables used in such models have a physical basis or are statistically significant is not always apparent.

Correlative models can be split according to the type of fluid being modelled: gas, dead oil, saturated oil or undersaturated oil. Different correlations exist for each of these scenarios. Shown below are selected correlations for gas and oil based on the approaches outlined by Bergman and Sutton. Whilst there are myriad correlations that have been published, these were based on perhaps the largest measured database and were amongst the first to be implemented into Pyrus. As such they form a good basis against which to compare the results of other methods and measured data.

#### Gas Viscosity Correlations

Often in laboratory reports the gas viscosity is not actually measured, and instead a calculated viscosity is shown. For gas fluids, a common viscosity correlation used is that of Lee, Gonzalez and Eakin (1966). According to their paper, this equation is based on the semi-empirical form of equation proposed by Starling and Ellington (1964) which itself was based on the earlier theory of viscosity presented by Born and Green (1947). This splits viscosity into a combination of kinetic and intermolecular force contributions. The [Lee, Gonzalez and Eakin (LGE) equation](https://wiki.whitson.com/bopvt/visc_correlations/#lee-correlation) can be applied to both mixtures and pure components.

Improvements to this form of viscosity correlation were made by Londono et al (2002) who re-regressed the equations against a larger dataset, and Sutton (2007) who adapted the equation to account for gas-condensate fluids for which viscosity is not well predicted by the method of Lee et al. The original Lee, Gonzalez and Eakin equation can be compared to these updated versions in the form:

$$\mu_g=\mu_{gsc}\exp{[X\rho^{Y}]}$$

With:

$$\mu_{gsc}=\frac{10^{-4}K}{\xi}$$

$$K_{orig}=\frac{(k_1+k_2M_w)T^{k_3}}{k_4+k_5M_w+T}$$

$$K_{alt}=0.807{T_{r}}^{0.618}-0.357e^{(-0.449T_{r})}+0.340e^{(-4.058T_{r})}+0.018$$

$$X=x_1+\left[\frac{x_2}{T}\right]+x_3M_w$$

$$Y=y_1+y_2X$$

Where:

 - _&mu;<sub>g</sub>_ = gas dynamic viscosity (cP)
 - _&mu;<sub>gsc</sub>_ = dilute gas mixture viscosity at low (e.g. atmospheric) pressure (cP)
 - _M<sub>w</sub>_ = molecular weight of gas mixture (lbm/lbm-mol)
 - _T_ = absolute temperature (K)
 - _T<sub>r</sub>_ = reduced temperature $$\frac{T}{T_c}$$ (dimensionless)
 - _T<sub>c</sub>_ = critical temperature (K)
 - _&rho;_ = density at temperature and pressure (g/cc)
 - _&xi;_ = viscosity-reducing parameter (cP<sup>-1</sup>)
 - _P<sub>c</sub>_ = critical pressure (atm)

Coefficients for the different forms of this correlation are given as:

| Parameter | Lee, Gonzalez and Eakin | Londono | Sutton |
| --- | --- | --- | --- |
| K | _K<sub>orig</sub>_ | _K<sub>orig</sub>_ | _K<sub>alt</sub>_ |
| _&xi;_ | 1 | 1 | $$\xi=0.9490\left(\frac{T_c}{M_w^3P_{c}^4}\right)^{1/6}$$ |
| _k<sub>1</sub>_ | 9.379 | 16.7175 |  |
| _k<sub>2</sub>_ | 0.01607 | 0.0419188 |  |
| _k<sub>3</sub>_ | 1.5 | 1.40256 |  |
| _k<sub>4</sub>_ | 209.2 | 212.209 |  |
| _k<sub>5</sub>_ | 19.26 | 18.1349 |  |
| _x<sub>1</sub>_ | 3.448 | 2.12574 | 3.47 |
| _x<sub>2</sub>_ | 986.4 | 2063.71 | 1588 |
| _x<sub>3</sub>_ | 0.01009 | 0.011926 | 0.0009 |
| _y<sub>1</sub>_ | 2.447 | 1.09809 | 1.66378  |
| _y<sub>2</sub>_ | -0.2224 | -0.0392851 | -0.04679 |

[code snippets]

### Theoretical Models

It was Maxwell who first derived a probability distribution to describe molecular velocities. Boltzmann's equation builds on this to describe the non-equilibrium behaviour of a gas and can be used to determine how macroscopic physical quantities of a gas, such as temperature and momentum, change when a gas is in motion. From this equation it is possible to derive equations that describe transport coefficients such as viscosity in terms of molecular parameters. One such set of equations are those associated with Chapman-Enskog theory. The first-order solution for viscosity is:

$$\mu=\frac{26.69(MT)^{1/2}}{\sigma^2\Omega_v}$$

Where:

 - _&mu;_ = gas viscosity (&micro;P)
 - _M_ = molecular weight (g/mol)
 - _T_ = temperature (K)
 - _&sigma;_ = hard-sphere diameter (&#8491;)
 - _&Omega;<sub>v</sub>_ = viscosity collision integral
 


### Corresponding States Models

The correlation models above calculate viscosity at a given temperature and pressure. An alternative approach is to first determine the gas viscosity at a known reference condition such as atmospheric conditions, and to then adjust the reference viscosity to the target temperature and pressure. By using reduced temperature and pressure the corresponding states principle (CSP), that gas will behave similarly under the same reduced conditions, can be exploited. 

Methods that are based on corresponding states include:

 1. **Comings et al (1944)**: This graphical method was an early industry standard.
 2. **Pedersen et al (1984, 1987)**: Uses a modified Benedict-Webb-Rubin virial equation of state for methane reference fluid.
 3. **Lucas (1974)**:

Comings et al (1944) expressed the viscosity ratio as a function of the reduced pressure and reduced temperature.

$$\frac{\mu}{\mu_{1atm}}=f(p_r, T_r)$$

Where:

 - _P<sub>r</sub>_ = reduced pressure [pressure (absolute units) / critical pressure (absolute units)] (dimensionless)
 - _T<sub>r</sub>_ = reduced temperature [temperature (absolute units) / critical temperature (absolute units)] (dimensionless)
 - _&mu;_ = viscosity of gas at reduced temperature _T<sub>r</sub>_ and reduced pressure _P<sub>r</sub>_ (cP)
 - _&mu;<sub>1atm</sub>_ = viscosity of gas at atmospheric pressure and at temperature _T<sub>r</sub>_ (cP)

Comings et al created various plots which allow the reduced conditions to be translated to a viscosity ratio. Carr et al (1954) extended the methodology to mixtures. In place of critical temperature and critical pressure for a component, the pseudo-critical temperature and pseudo-critical pressure for the gas mixture was obtained using Kay's mixing rules. The dilute gas mixture viscosity was determined using the method of Herning and Zipperer (1936).

$$\mu_m=\frac{\sum_{i=1}^{n}\mu_iz_i\sqrt{M_i}}{\sum_{i=1}^{n}z_i\sqrt{M_i}}$$

Where:

 - _&mu;<sub>i</sub>_ = component dynamic viscosity (cP)
 - _z<sub>i</sub>_ = component mol fraction (dimensionless)
 - _M<sub>i</sub>_ = component molecular weight (lbm/lbm-mol)

Until the advent of the Lee, Gonzalez and Eakin correlation, this method was often used for calculating gas viscosity by laboratories. A comparison of some measured data for pure components, binary mixtures and hydrocarbon mixtures at a range of reduced temperatures is shown in the figure below.

<figure>
	<a href="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" data-lightbox="image-2" data-title=".">
		<img src="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" alt="."/>
	</a>
	<figcaption><strong>Figure 2: .</strong></figcaption>
</figure>

Another approach is that devised by [Pedersen et al (1984, 1987)]({{ site.url }}/analysis/viscosity-pedersen/). Lindeloff et al (2003) showed how this approach could be combined with a heavy oil viscosity correlation to extend to CSP method from application of gas through to heavy oil. The Lindeloff-Pedersen CSP approach can be applied across the full range of hydrocarbons from light gases through to heavy oils.

[Lucas]

Need references...

### Residual Viscosity Models

Residual viscosity is defined as the difference between viscosity at a target temperature and pressure and the viscosity of the dilute gas phase (usually at 1 atm pressure and the same target temperature) = _&mu; &minus; &mu;<sup>*</sup>_ for pure fluids.

Methods that are based on residual viscosity include:

 1. **Jossi, Stiel and Thodos (1962)**: This method is often encountered in the Lohrenz-Bray-Clark viscosity approach.
 2. **TRAPP**: The **TRA**nsport **P**roperties **P**rediction method was first outlined by [???] (19??) and an improved method was given by Huber and Hanley (1996).

Jossi, Stiel and Thodos (1962) introduced an alternative approach based on the residual viscosity. It was determined that the residual viscosity should be a function of the density and dimensional constants specific to each fluid. Through application of dimensional analysis and computer-aided polynomial regression (a novelty at that time), an analytical relationship was found:

$$\left[\left({\mu-\mu^{*}}\right)\xi+10^{-4}\right]^{1/4}=a_1+a_2\rho_r+a_3\rho_r^2+a_4\rho_r^3+a_5\rho_r^4$$

With:

$$\xi=\frac{T_c^{1/6}}{M_w^{1/2}p_c^{2/3}}$$

Where:

 - _&mu;_ = dynamic viscosity (cP)
 - _&mu;<sup>*</sup>_ = viscosity at normal pressures (0.1 to 5 atm) (cP)
 - (_&mu;_ &minus; _&mu;<sup>*</sup>_) = residual viscosity (cP)
 - _&xi;_ = viscosity-reducing parameter &#8776; 1 / _&mu;<sub>c</sub>_ (cP<sup>-1</sup>)
 - _&rho;<sub>r</sub>_ = reduced density [density / critical density] (dimensionless)
 - _T<sub>c</sub>_ = critical temperature (K)
 - _p<sub>c</sub>_ = critical pressure (atm)
 - _M<sub>w</sub>_ = molecular weight (g/mol)

It is noted that this relationship holds for most fluids where critical compressibility was between 0.269 to 0.294. These fluids include argon, nitrogen, oxygen, carbon dioxide, sulphur dioxide, methane, ethane, propane, i-butane, n-butane, and n-pentane. Fluids with critical compressibilities outside of this range (hydrogen, water and ammonia) exhibited similar trends, but with deviation from the principal trend in the lower density region (_P_<sub>r</sub> < 1.0). The coefficients for the different fluids are:

| Parameter | Normal Fluids | Hydrogen | Ammonia | Water |
| --- | --- | --- | --- | --- |
| _z<sub>c</sub>_ | 0.269 to 0.294 | 0.305 | 0.242 | 0.231 |
| _a<sub>1</sub>_ | 0.10230 | 0.10616 | 0.10670 | 0.10721 |
| _a<sub>2</sub>_ | 0.023364 | -0.042426 | 0.022655 | 0.040646 |
| _a<sub>3</sub>_ | 0.058533 | 0.17553 | 0.035749 | 0.0026282 |
| _a<sub>4</sub>_ | -0.040758 | -0.12295 | -0.032153 | -0.0054430 |
| _a<sub>5</sub>_ | 0.0093324 | 0.028149 | 0.0089998 | 0.0017979 |

<div class="notice-warning">The Jossi et al residual viscosity could also be described as a corresponding states approach since it is based on a reduced increase in viscosity above the dilute gas viscosity in terms of the reduced density, and this is similar for many fluids. Here we distinguish between methods that model the viscosity ratio, &mu; / &mu;<sup>*</sup> = <i>f</i>(T<sub>r</sub> , p<sub>r</sub>), as corresponding states models, and those that model the additional viscosity, &mu; = &mu;<sup>*</sup> + <i>f</i>(T<sub>r</sub> , p<sub>r</sub>), as residual viscosity models.</div>

The [Lohrenz-Bray-Clark (LBC) correlation](https://wiki.whitson.com/bopvt/visc_correlations/#lohrenz-bray-clark-correlation) is often used in the oil and gas industry. It is incorporated into almost all software packages that deal with fluid properties, not because it is necessarily the best approach, but because it is expected that the method should be available. As such it is almost universally applied. Furthermore, it is intended to calculate the viscosity of both gases and liquids, at high and low temperature and pressure. A [separate article investigating the shortcomings of the Lohrenz-Bray-Clark method]({{ site.url }}/analysis/viscosity-lbc/) goes into more detail on this method. Interestingly the original procedure proposed by Lohrenz, Bray and Clark applied a different viscosity technique depending on whether the fluid is a gas or a liquid. For gases, the graphical corresponding states method of Carr et al for mixtures was applied, but with a digitised graphical chart that facilitated computerised interpolation.

<div class="notice-info">The LBC approach actually describes a methodology for combining existing methods to determine low-pressure viscosity, combining component viscosities to create a low-pressure mixture viscosity, and finally how to determine the viscosity at the actual pressure depending on whether the fluid is a gas or a liquid. The equation that is commonly referred to as the Lohrenz-Bray-Clark viscosity equation is actually the Jossi, Stiel and Thodos (1962) equation.</div>

[TRAPP]

### Group Contribution Method Techniques

Although there are myriad equations and methods that have been developed to predict fluid viscosity, there is no universally accepted solution that accurately predicts viscosity across all fluid types. Many methods can be shown to be reasonably accurate if the pure component viscosities, or low-pressure mixture viscosity can be accurately measured. In other words, in the presence of a datapoint anchor, the principles that govern variation in viscosity with temperature and pressure are understood and viscosity at other temperatures and pressures can be reliably predicted.

But what if there is no laboratory measurement from which to 'tune' the viscosity model?

Group contribution methods have been successfully applied to modelling of various chemical properties, and viscosity is no exception. By breaking the molecule into smaller constituent groups, each of which contributes a certain amount to the final property through its molecular size and shape, the component viscosity can be predicted in the absence of measured data.

Techniques that are based on group contribution methods include:

 1. **Reichenberg (1974, 1977, 1979)**:

[Reichenberg]

## References

 - Alani, G. H. and Kennedy, H. T. 1960. Volumes of Liquid Hydrocarbons at High Temperatures and Pressures. _Trans._ **219** (01): 288-292. SPE-1399-G. [https://doi.org/10.2118/1399-G](https://doi.org/10.2118/1399-G).
 - Ali, J. K. 1991. Evaluation of Correlations for Estimating the Viscosities of Hydrocarbon Fluids. _J Pet Sci Eng_ **5**: 351-369. [https://doi.org/10.1016/0920-4105(91)90053-P](https://doi.org/10.1016/0920-4105(91)90053-P).
 - Andrade, E. N. da C. 1930. The Viscosity of Liquids. _Nature_ **125**: 309-310. [https://doi.org/10.1038/125309b0](https://doi.org/10.1038/125309b0).
 - Bergman, D. F. and Sutton, R. P., 2006. Undersaturated Oil Viscosity Correlation for Adverse Conditions. Paper presented at the SPE Annual Technical Conference and Exhibition, San Antonio, Texas, USA, 24-27 September 2006. SPE-103144-MS. [https://doi.org/10.2118/103144-MS](https://doi.org/10.2118/103144-MS).
 - Bergman, D. F. and Sutton, R. P., 2007. A Consistent and Accurate Dead-Oil-Viscosity Method. Paper presented at the SPE Annual Technical Conference and Exhibition, Anaheim, California, USA, 11-14 November 2007. SPE-110194-MS. [https://doi.org/10.2118/110194-MS](https://doi.org/10.2118/110194-MS).
 - Bergman, D. F. and Sutton, R. P., 2007. An Update to Viscosity Correlations for Gas-Saturated Crude Oils. Paper presented at the SPE Annual Technical Conference and Exhibition, Anaheim, California, USA, 11-14 November 2007. SPE-110195-MS. [https://doi.org/10.2118/110195-MS](https://doi.org/10.2118/110195-MS).
 - Born, M. and Green, H. S. 1947. A General Kinetic Theory of Liquids III: Dynamical Properties. _Proc_, Royal Society, London, England, 1 September, **190** (1023): 455–474. [https://doi.org/10.1098/rspa.1947.0088](https://doi.org/10.1098/rspa.1947.0088).
 - Carr, N. L., Kobayashi, R., and Burrows, D. B. 1954. Viscosity of Hydrocarbon Gases Under Pressure. _J Pet Technol_ **6** (10): 47-55. SPE-297-G. [https://doi.org/10.2118/297-G](https://doi.org/10.2118/297-G).
 - [Comings, E. W., Mayland, B. J., and Egly, R. S. 1944. The Viscosity of Gases at High Pressures. _University of Illinois Engineering Experiment Station Bulletin No. 354_.](http://hdl.handle.net/2142/4478)
 - [Chapman, S. and Cowling, T. G. 1970. _The Mathematical Theory of Non-Uniform Gases_, third edition. Cambridge: Cambridge University Press.](https://www.cambridge.org/us/universitypress/subjects/mathematics/fluid-dynamics-and-solid-mechanics/mathematical-theory-non-uniform-gases-account-kinetic-theory-viscosity-thermal-conduction-and-diffusion-gases)
 - Chung, T. H., Ajlan, M., Lee, L. L., and Starling, K. E. 1988. Generalized Multiparameter Correlation for Nonpolar and Polar Fluid Transport Properties. _Ind Eng Chem Res_ **27**: 671-679. [https://doi.org/10.1021/ie00076a024](https://doi.org/10.1021/ie00076a024).
 - De Guzman, J. 1913. Relation Between Fluidity and Heat of Fusion. _Anales Soc Espan Fis Y Quim_ **11**: 353-362.
 - Huber, M. L. and Hanley H. J. M. 1996. The Corresponding-States Principle: Dense Fluids. In _Transport Properties of Fluids, Their Correlation, Prediction and Estimation_, ed. J. Millat, J. H. Dymond, and  C. A. Nieto de Castro, Chap. 12, 283-295. Cambridge: Cambridge University Press. [https://doi.org/10.1017/CBO9780511529603](https://doi.org/10.1017/CBO9780511529603)
 - Isdale, J. D. 1979. In _Symposium on Transport Properties of Fluids and Fluid Mixtures, Their Measurement, Estimation, Correlation, and Use_. East Kilbride, Glasgow, Scotland: Nat. Engineering Laboratory.
 - Jossi, J. A., Stiel, L. I., and Thodos, G. 1962. The Viscosity of Pure Substances in the Dense Gaseous and Liquid Phases. _AIChE Journal_ **8**: 59-63. [https://doi.org/10.1002/aic.690080116](https://doi.org/10.1002/aic.690080116).
 - Lee, A. L., Gonzalez, M. H., and Eakin, B. E. 1966. The Viscosity of Natural Gas. _J Pet Technol_ **18** (8): 997-1000. SPE-1340-PA. [https://doi.org/10.2118/1340-PA](https://doi.org/10.2118/1340-PA).
 - Lindeloff, N., Pedersen, K. S., Rønningsen, H.P., and Milter, J. 2003. The Corresponding States Viscosity Model Applied to Heavy Oil Systems. Paper presented at the Canadian International Petroleum Conference, Calgary, Alberta, Canada, 10-12 June. PETSOC-2003-150. [https://doi.org/10.2118/2003-150](https://doi.org/10.2118/2003-150).
 - Lohrenz, J., Bray, B. G., and Clark, C. R. 1964. Calculating Viscosities of Reservoir Fluids From Their Compositions. _J Pet Technol_ **16** (10): 1171–1176. SPE-915-PA. [https://doi.org/10.2118/915-PA](https://doi.org/10.2118/915-PA).
 - Londono, F. E., Archer, R. A., and Blasingame, T. A. 2002. Simplified Correlations for Hydrocarbon Gas Viscosity and Gas Density — Validation and Correlation of Behavior Using a Large-Scale Database. Paper presented at the SPE Gas Technology Symposium, Calgary, Alberta, Canada, April 2002. SPE-75721-MS. [https://doi.org/10.2118/75721-MS](https://doi.org/10.2118/75721-MS).
 - Lucas, K. 1974. Ein einfaches Verfahren zur Berechnung der Viskositat von Gasen und Gasgemischen (A Simple Method for Calculating the Viscosity of Gases and Gas Mixtures). _Chem Ing Tech_ **46** (4): 157. [https://doi.org/10.1002/cite.330460413](https://doi.org/10.1002/cite.330460413).
 - Lucas, K. 1981. Die Druckabhängigkeit der Viskosität von Flüssigkeiten – eine einfache Abschätzung (The Pressure Dependence of the Viscosity of Liquids - a Simple Estimation). _Chem Ing Tech_ **53** (12): 959-960. [https://doi.org/10.1002/cite.330531209](https://doi.org/10.1002/cite.330531209).
 - Pedersen, K. S., Christensen, P. L., and Shaikh, J. A. 2024. _Phase Behavior of Petroleum Reservoir Fluids_, third edition. Boca Raton: CRC Press.
 - Pedersen, K. S., Fredenslund, A., Christensen, P. L., and Thomassen, P. 1984. Viscosity of Crude Oils. _Chem Eng Sci_ **39** (6): 1011-1016. [https://doi.org/10.1016/0009-2509(84)87009-8](https://doi.org/10.1016/0009-2509(84)87009-8).
 - Pedersen, K. S. and Fredenslund, A. 1987. An Improved Corresponding States Model for the Prediction of Oil and Gas Viscosities and Thermal Conductivities. _Chem Eng Sci_ **42** (1): 182–186. [https://doi.org/10.1016/0009-2509(87)80225-7](https://doi.org/10.1016/0009-2509(87)80225-7).
 - Poling, B. E., Prausnitz, J. M., and O’Connell, J. P. 2004. _The Properties of Gases and Liquids_, fifth edition. New York: McGraw-Hill Book Co.
 - Starling, K. E. and Ellington, R. T. 1964. Viscosity Correlations for Nonpolar Dense Fluids. _AIChE Journal_ **10**: 11-15. [https://doi.org/10.1002/aic.690100112](https://doi.org/10.1002/aic.690100112).
 - Stephen K. and Lucas K. 1979. _Viscosity of Dense Fluids_. New York: Springer. [https://doi.org/10.1007/978-1-4757-6931-9](https://doi.org/10.1007/978-1-4757-6931-9).
 - Sutton, R. P. 2007. Fundamental PVT Calculations for Associated and Gas/Condensate Natural Gas Systems. Paper presented at the SPE Annual Technical Conference and Exhibition, Dallas, Texas, USA, October 2005. SPE-97099-MS. [https://doi.org/10.2118/97099-MS](https://doi.org/10.2118/97099-MS).