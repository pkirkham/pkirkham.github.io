---
layout: article
title: Viscosity Modelling
modified:
categories: analysis
excerpt: Approach to viscosity prediction used in Pyrus based on group contribution methods.
tags: [pyrus_suite, dynamic_model, simulation, software, programming, simulation_deck, fluid_properties, oil, gas, water, reservoir_engineering, viscosity, corresponding_states, residual_viscosity]
image:
  feature: feature-viscosity-modelling-1024x256.jpg
  teaser: teaser-viscosity-modelling-400x250.jpg
  thumb:
comments: false
mathjax: true
---

Viscosity governs how easily a fluid will flow and can be conceptualised as the internal fluid friction which opposes any dynamic changes to fluid flow. This makes it a very important parameter in dynamic simulation of reservoir models. Great effort has gone into understanding how viscosity can be altered to improve oil recovery, for example with thermal methods such as steam flooding that help reduce the viscosity of heavy oil and thus aid recovery.

Dynamic viscosity _&mu;_ is the absolute measure of how easily a fluid will flow. It has a value of about 1.0 centipoise (cP) for water at 20 &deg;C which makes the unit convenient to interpret. Sometimes kinematic viscosity is reported, which is simply the viscosity divided by the density (_&mu;_ / _&rho;_) and is typically shown with units of stokes (St). Note that one centistoke (cSt) is 1 cP divided by 1,000 kg/m<sup>3</sup> which is approximately the density of water. Thus for water, the dynamic viscosity is close to 1 cP and the kinematic viscosity is close to 1 cSt.

Viscosity can be [measured in a laboratory using a viscometer](https://en.wikipedia.org/wiki/Viscometer) or estimated using correlations or other similar analytical methods. Note that viscosity reported in laboratory reports may not necessarily be measured.

In Pyrus, where fluid composition or type may be estimated, but actual measurements do not exist, it is necessary to implement a method to predict viscosity as accurately as possible. This assists with generation of reliable black oil tables for use in reservoir simulators.

## Modelling Viscosity



### Viscosity Theory



<figure>
	<a href="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" data-lightbox="image-1" data-title=".">
		<img src="{{ site.url }}/images/Analysis/viscosity-modelling/viscosity-figure2.png" alt="."/>
	</a>
	<figcaption><strong>Figure 1: .</strong></figcaption>
</figure>



## Viscosity Models

Generally there are several types of viscosity model.

 1. **Correlation**: Correlations have been devised from large datasets to predict viscosity for gases and liquids against common field measurements. These approaches require that high level fluid parameters such as the gravity and saturation pressures are known. For well behaved typical oil and gas fluids, these approaches can be considered robust.
 2. **Theoretical**: Theoretical models combine effects of kinetic theory and intermolecular forces. Most approaches are based on the equations derived by Chapman and Enskog. Since this approach is based on the behaviour of interactions between colliding molecules, and the attractions (or repulsions) that exist between them, it is only applicable to modelling of gas viscosity.
 3. **Corresponding States**: Based on the principle that fluids will exhibit similar thermodynamic behaviour at the same reduced temperature and reduced pressure, it is possible to create a model for viscosity based on a known fluid and extend this to other components or mixtures. It is the [theory of corresponding states](https://en.wikipedia.org/wiki/Theorem_of_corresponding_states) that underpins the well-known Standing and Katz z-factor chart which uses the reduced pressure and reduced temperature to determine the z-factor. In other words, for any gas at the same conditions relative to its critical point, will exhibit similar deviation from ideal gas behaviour.
 4. **Residual Viscosity**: A relationship between the density of a fluid and its viscosity exists. The difference between the viscosity and the viscosity of the fluid at atmospheric conditions is referred to as the residual viscosity. It can be shown that values of residual viscosity plotted against density fall onto a curve, and that this appeears to be independent of temperature.
 5. **Group Contribution Method**: Models based on group contribution methods can support predictive viscosity modelling if the composition of the fluid is known. These are typically used for low-temperature liquid viscosity approaches where corresponding states methods are less accurate. In contrast, group contribution methods take into consideration the effects of the chemical structure on viscosity.

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

#### Liquid Viscosity Correlations

For liquid hydrocarbons a different correlation is required. Oil viscosity depends not just on the temperature and pressure, but also the dissolved gas volume. This gives rise to three different correlations:

 1. **Dead oil viscosity**: The viscosity of an oil containing no dissolved gas.
 2. **Saturated oil viscosity**: The viscosity of an oil when saturated with the maximum volume of dissolved gas e.g., for an oil at or below bubblepoint pressure.
 3. **Undersaturated oil viscosity**: The viscosity of an oil containing dissolved gas with lower volume than the saturated capacity e.g., for an oil above the bubblepoint pressure.

Bergman and Sutton (2006, 2007) have published a set of correlations based on a large database of measurements and compared their results against other commonly applied correlations.

For dead oil the equations are based on the form introduced by Twu, which was updated using a larger database. The steps to determine dead oil viscosity are:

 1. Find absolute viscosity at 100 and 210 &deg;F.

    $$T_c^o=T_b\left(0.533272+1.91017\times10^{-4}T_b+7.79681\times10^{-8}T_b^2 \\
    -2.84376\times10^{-11}T_b^3+9.59468\times10^{27}T_b^{-13}\right)^{-1}$$

    $$\alpha=1-\frac{T_b}{T_c^o}$$

    $$\ln(\nu_2-0.152995)=2.40219-9.59688\alpha+3.45656\alpha^2-143.632\alpha^4$$

    $$\ln(\nu_1)=0.701254+1.38359\ln(v2)+0.103604\left[\ln(v2)\right]^2$$

    $$\gamma_o^o=0.843593-0.128624\alpha-3.36159\alpha^3-13749.5\alpha^{12}$$

    $$\Delta\gamma_o=\gamma_o-\gamma_o^o$$

    $$|x|=\left|2.68316-62.0863/T_b^{0.5}\right|$$

    $$f_2=|x|\Delta\gamma_o-47.6033\Delta\gamma_o^2/T_b^{0.5}$$

    $$\ln\left(\nu_{210}+232.442/T_b\right)=\ln\left(\nu_2+232.442/T_b\right)\left(\frac{1+2f_2}{1-2f_2}\right)^2$$

    $$f_1=0.980633|x|\Delta\gamma_o-47.6033\Delta\gamma_o^2/T_b^{0.5}$$

    $$\ln\left(\nu_{100}+232.442/T_b\right)=\ln\left(\nu_1+232.442/T_b\right)\left(\frac{1+2f_1}{1-2f_1}\right)^2$$

    With:

    $$T_b = (K_w\gamma_o)^3$$

    Where:

     - _T<sub>b</sub>_ = average boiling point temperature (&deg;R)
     - _K<sub>w</sub>_ = Watson characterisation factor
     - _&gamma;<sub>o</sub>_ = oil specific gravity

 2. Convert kinematic viscosity to dynamic viscosity.

    $$\rho_{o_{60}}=0.999012\gamma_o$$

    $$\rho_{o_{100}}=\rho_{o_{60}}\times VCF\left[\rho_{o_{60}}, 100 - 60\right]$$

	$$\mu_{od_{100}}=\rho_{o_{100}}\nu_{100}$$

    $$\rho_{o_{210}}=\rho_{o_{60}}\times VCF\left[\rho_{o_{60}}, 210 - 60\right]$$

	$$\mu_{od_{210}}=\rho_{o_{210}}\nu_{210}$$

    With:

    $$VCF\left[\rho_{o_{60}}, T\right]=e^{\left[-\alpha_{60}\Delta T(1+0.8\alpha_{60}\Delta T)\right]}$$

    $$\alpha_{60}=\frac{2.5042\times10^{-4}+8.302\times10^{-5}\rho_{o_{60}}}{\rho_{o_{60}}^2}$$

    $$\Delta T=T-60$$

    Where:

     - _VCF[&rho;<sub>o<sub>60</sub></sub>, T]_ = volume correction factor at temperature _T_
     - _&alpha;_ = reduced boiling point temperature
     - _T_ = temperature (&deg;F)

 3. Bergman method to find absolute viscosity based on temperature.

    $$\mu_{od}=\exp\left[\exp\left(\ln(\ln(\mu_{od_{100}}+1))+B\ln(T+310)-\ln(410))\right)\right]-1$$

    With:

    $$B=\ln(\ln(\mu_{od_{210}}+1))-\frac{\ln(\ln(\mu_{od_{100}}+1))}{ln(520)-ln(410)}$$

    Where:

     - _&mu;<sub>od</sub>_ = dead oil viscosity (cP)

Using the dead oil viscosity, it is now possible to determine the saturated oil viscosity. The form of the equation is based on Chew and Connally using the form:

$$\mu_{ob}=A\mu_{od}^B$$

With:

$$A=\frac{1}{1+\left(\frac{R_s}{344.198}\right)^{0.855344}}$$

$$B=0.382323+\frac{(1-0.382323)}{1+\left(\frac{R_s}{567.953}\right)^{0.819326}}$$

Where:

 - _R<sub>s</sub>_ = solution gas-oil ratio (SCF/STB)
 - _&mu;<sub>ob</sub>_ = saturated oil viscosity, (cP)
 - _&mu;<sub>od</sub>_ = dead oil viscosity, (cP)

Finally we can now determine the undersaturated oil viscosity. The form of the equation is based on Barus with an additional &beta; exponent for the pressure term to account for the slight downward curvature observed at higher pressure differentials.

$$\mu_o=\mu_{ob}e^{\alpha\left(p-p_b\right)^\beta}$$

With:

$$\alpha=6.5698\times10^{-7}\ln\left(\mu_{ob}\right)^2-1.48211\times10^{-5}\ln\left(\mu_{ob}\right)+2.27877\times10^{-4}$$

$$\beta=2.24623\times10^{-2}\ln\left(\mu_{ob}\right)+0.873204$$

Where:

 - _&mu;_ = undersaturated oil viscosity, (cP)
 - _&mu;<sub>ob</sub>_ = saturated oil viscosity, (cP)
 - _p_ = pressure (psia)
 - _p<sub>b</sub>_ = bubblepoint pressure, (psia)

### Corresponding States Models

The correlation models above calculate viscosity at a given temperature and pressure. An alternative approach is to first determine the gas viscosity at a known reference condition such as atmospheric conditions, and to then adjust the reference viscosity to the target temperature and pressure. By using reduced temperature and pressure the corresponding states principle (CSP), that gas will behave similarly under the same reduced conditions, can be exploited. Comings et al (1944) expressed the viscosity ratio as a function of the reduced pressure and reduced temperature.

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

Until the advent of the Lee, Gonzalez and Eakin correlation, this method was often used for calculating gas viscosity by laboratories.

Another approach is that devised by Pedersen et al (1984, 1987). Lindeloff et al (2003) showed how this approach could be combined with a heavy oil viscosity correlation to extend to CSP method from application of gas through to heavy oil.

### Residual Viscosity Models

Jossi, Stiel and Thodos (1962) introduced an alternative approach based on the residual viscosity. Residual viscosity is defined as the difference between viscosity at a target temperature and pressure and the viscosity of the dilute gas phase (usually at 1 atm pressure and the same target temperature) = _&mu; &minus; &mu;<sup>*</sup>_ for pure fluids. It was determined should be a function of the density and dimensional constants specific to each fluid. Through application of dimensional analysis and computer-aided polynomial regression (a novelty at that time), an analytical relationship was found:

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

The [Lohrenz-Bray-Clark (LBC) correlation](https://wiki.whitson.com/bopvt/visc_correlations/#lohrenz-bray-clark-correlation) is often used in the oil and gas industry. It is incorporated into almost all software packages that deal with fluid properties, not because it is necessarily the best approach, but because it is expected that the method should be available. As such it is almost universally applied. Furthermore, it is intended to calculate the viscosity of both gases and liquids, at high and low temperature and pressure. The procedure used depends on whether the fluid is a gas or a liquid. For gases, previously published correlations were used. For liquids, it was believed that this was a novel procedure at the time for calculation of viscosity from compositions up to heptanes-plus fraction.

### Group Contribution Method Models

Although there are myriad equations and methods that have been developed to predict fluid viscosity, there is no universally accepted solution that accurately predicts viscosity across all fluid types.

Early research into fluid viscosity by De Guzman (1913) and later by Andrade (1930) revealed that fluid viscosity _&mu;_ is related to temperature _T_ according to the following formula.

$$\mu = Ae^{B / T}$$

Several variations and improvements to this formulation have been developed. Orrick and Erbar (1973) proposed a predictive formula for a single component dynamic viscosity using a group contribution method to determine the co-efficients:

$$ln(\mu/M_{w}d_{20}) = A + B / T$$

With:

$$A=-6.95 + \sum_{i}^{}n_{i}A_i$$

$$B=275 + \sum_{i}^{}n_{i}B_i$$

$$d_{20}=S_f-0.0045 \times (2.34 - (1.9S_f))$$

Where:

 - _&mu;<sup>*</sup>_ = low-pressure component dynamic viscosity
 - _T_ = temperature
 - _i_ = group type for _N_ recognised groups
 - _n<sub>i</sub>_ = the number of groups of that type
 - _M<sub>w</sub>_ = molecular weight
 - _d<sub>20</sub>_ = density at 20 &deg;C
 - _S<sub>f</sub>_ = fluid specific gravity (water = 1.0)

Group co-efficients for hydrocarbon components are:

| Groupnum | Group | A<sub>i</sub> Co-efficient | B<sub>i</sub> Co-efficient |
| --- | --- | --- | --- |
| 1 | -CH3 | -0.21 | 99.0 |
| 2 | -CH2- | -0.21 | 99.0 |
| 3 | >CH- | -0.15 | 35.0 |
| 4 | >C< | -1.2 | 400.0 |
| 5 | Aromatic Ring | 0.0 | 20.0 |
| 6 | Cyclic Ring | -0.45 | 250.0 |

The Orrick and Erbar viscosity applies to pure components only, and cannot be applied to mixtures. Furthermore it is not recommended for use at high temperatures or pressure above atmospheric.

[Other GCM method? Reichenberg (1974, 1977, 1979) for gas. Sastri-Rao (1992) for liquid.]

Fu (2014) outlines an approach to calculating mixture viscosity where the GCM method of Orrick and Erbar is combined with the Grunberg and Nissan (1949) mixing method. This mixing method was adapted by Isdale (1979) to introduce a GCM approach to determine the mixing interaction parameter. Thus a similar methodology to LBC can be constructed using only GCM methods in place of the low pressure pure-component viscosity correlations and the Herning and Zipper (1936) mixing rule.

[Grunberg-Nissan mixing with Isdale mixing rule.]

[Similar approach to LBC using GCM low-pressure mixture viscosity and then applying Lucas correlation for gas or liquid to get high-pressure viscosity.]

## Comparison Against Published Datasets

 - Comings (carbon dioxide, ethylene, methane, propane)
 - Poling (multiple pure compounds gas + liquid)
 - Pedersen (numerous examples in book)
 - Lindeloff (8x oils)

## References

 - Alani, G. H. and Kennedy, H. T. 1960. Volumes of Liquid Hydrocarbons at High Temperatures and Pressures. _Trans._ **219** (01): 288-292. SPE-1399-G. [https://doi.org/10.2118/1399-G](https://doi.org/10.2118/1399-G).
 - Ali, J. K. 1991. Evaluation of Correlations for Estimating the Viscosities of Hydrocarbon Fluids. _J. Pet. Sci. Eng._ **5**: 351-369. [https://doi.org/10.1016/0920-4105(91)90053-P](https://doi.org/10.1016/0920-4105(91)90053-P).
 - Andrade, E. N. da C. 1930. The Viscosity of Liquids. _Nature_ **125**: 309-310. [https://doi.org/10.1038/125309b0](https://doi.org/10.1038/125309b0).
 - Bergman, D. F. and Sutton, R. P., 2006. Undersaturated Oil Viscosity Correlation for Adverse Conditions. Paper presented at the SPE Annual Technical Conference and Exhibition, San Antonio, Texas, USA, 24-27 September 2006. SPE-103144-MS. [https://doi.org/10.2118/103144-MS](https://doi.org/10.2118/103144-MS).
 - Bergman, D. F. and Sutton, R. P., 2007. A Consistent and Accurate Dead-Oil-Viscosity Method. Paper presented at the SPE Annual Technical Conference and Exhibition, Anaheim, California, USA, 11-14 November 2007. SPE-110194-MS. [https://doi.org/10.2118/110194-MS](https://doi.org/10.2118/110194-MS).
 - Bergman, D. F. and Sutton, R. P., 2007. An Update to Viscosity Correlations for Gas-Saturated Crude Oils. Paper presented at the SPE Annual Technical Conference and Exhibition, Anaheim, California, USA, 11-14 November 2007. SPE-110195-MS. [https://doi.org/10.2118/110195-MS](https://doi.org/10.2118/110195-MS).
 - Born, M. and Green, H. S. 1947. A General Kinetic Theory of Liquids III: Dynamical Properties. _Proc._, Royal Society, London, England, 1 September, **190** (1023): 455–474. [https://doi.org/10.1098/rspa.1947.0088](https://doi.org/10.1098/rspa.1947.0088).
 - Carr, N.L., Kobayashi, R., and Burrows, D.B. 1954. Viscosity of Hydrocarbon Gases Under Pressure. _J Pet Technol_ **6** (10): 47-55. SPE-297-G. [https://doi.org/10.2118/297-G](https://doi.org/10.2118/297-G).
 - [Comings, E. W., Mayland, B. J., and Egly, R. S. 1944. The Viscosity of Gases at High Pressures. _University of Illinois Engineering Experiment Station Bulletin No. 354_.](http://hdl.handle.net/2142/4478)
 - [Chapman, S. and Cowling, T. G. 1970. _The Mathematical Theory of Non-Uniform Gases_, third edition. Cambridge: Cambridge University Press.](https://www.cambridge.org/us/universitypress/subjects/mathematics/fluid-dynamics-and-solid-mechanics/mathematical-theory-non-uniform-gases-account-kinetic-theory-viscosity-thermal-conduction-and-diffusion-gases)
 - De Guzman, J. 1913. Relation Between Fluidity and Heat of Fusion. _Anales Soc. Espan. Fis. Y. Quim_ **11**: 353-362.
 - Fu, Z. 2014. _Group Contribution Modeling of Physical Properties During Urethane Reaction_. MS thesis, University of Missouri-Columbia (May, 2014).
 - Grunberg, L. and Nissan, A. H. 1949. A Mixture Law for Viscosity. _Nature_ **164**: 799-800. [https://doi.org/10.1038/164799b0](https://doi.org/10.1038/164799b0).
 - Herning, F. and Zipperer, L. (1936). Beitrag zur Berechnung der Zähigkeit Technischer Gasgemische aus den Zähigkeitswerten der Einzelbestandteile (Calculation of the Viscosity of Technical Gas Mixtures from Viscosity of the individual Gases), _Gas und Wasserfach_ **79**: 49–54, 69-73.
 - Isdale, J. D. 1979. In _Symposium on Transport Properties of Fluids and Fluid Mixtures, Their Measurement, Estimation, Correlation, and Use_. East Kilbride, Glasgow, Scotland: Nat. Engineering Laboratory.
 - Jossi, J. A., Stiel, L. I., and Thodos, G. 1962. The Viscosity of Pure Substances in the Dense Gaseous and Liquid Phases. _AIChE Journal_ **8**: 59-63. [https://doi.org/10.1002/aic.690080116](https://doi.org/10.1002/aic.690080116).
 - Lee, A. L., Gonzalez, M. H., and Eakin, B. E. 1966. The Viscosity of Natural Gas. _J Pet Technol_ **18** (8): 997-1000. SPE-1340-PA. [https://doi.org/10.2118/1340-PA](https://doi.org/10.2118/1340-PA).
 - Lindeloff, N., Pedersen, K. S., Rønningsen, H.P., and Milter, J. 2003. The Corresponding States Viscosity Model Applied to Heavy Oil Systems. Paper presented at the Canadian International Petroleum Conference, Calgary, Alberta, Canada, 10-12 June. PETSOC-2003-150. [https://doi.org/10.2118/2003-150](https://doi.org/10.2118/2003-150).
 - Lohrenz, J., Bray, B.G., and Clark, C.R. 1964. Calculating Viscosities of Reservoir Fluids From Their Compositions. _J Pet Technol_ **16** (10): 1171–1176. SPE-915-PA. [https://doi.org/10.2118/915-PA](https://doi.org/10.2118/915-PA).
 - Londono, F. E., Archer, R. A., and Blasingame, T. A. 2002. Simplified Correlations for Hydrocarbon Gas Viscosity and Gas Density — Validation and Correlation of Behavior Using a Large-Scale Database. Paper presented at the SPE Gas Technology Symposium, Calgary, Alberta, Canada, April 2002. SPE-75721-MS. [https://doi.org/10.2118/75721-MS](https://doi.org/10.2118/75721-MS).
 - Orrick, C. and Erbar, J. H. 1973. _Estimation of Viscosity for Organic Liquids_. Proposition Report, Oklahoma State University, Stillwater, Oklahoma (July, 1973).
 - Pedersen, K. S., Christensen, P. L., and Shaikh, J. A. 2024. _Phase Behavior of Petroleum Reservoir Fluids_, third edition. Boca Raton: CRC Press.
 - Pedersen, K. S., Fredenslund, A., Christensen, P. L., and Thomassen, P. 1984. Viscosity of Crude Oils. _Chem. Eng. Sci._ **39** (6): 1011-1016. [https://doi.org/10.1016/0009-2509(84)87009-8](https://doi.org/10.1016/0009-2509(84)87009-8).
 - Pedersen, K. S. and Fredenslund, A. 1987. An Improved Corresponding States Model for the Prediction of Oil and Gas Viscosities and Thermal Conductivities. _Chem. Eng. Sci._ **42** (1): 182–186. [https://doi.org/10.1016/0009-2509(87)80225-7](https://doi.org/10.1016/0009-2509(87)80225-7).
 - Poling, B. E., Prausnitz, J. M., and O’Connell, J. P. 2004. _The Properties of Gases and Liquids_, fifth edition. New York City: McGraw-Hill Book Co.
 - Starling, K. E. and Ellington, R. T. 1964. Viscosity Correlations for Nonpolar Dense Fluids. _AIChE Journal_ **10**: 11-15. [https://doi.org/10.1002/aic.690100112](https://doi.org/10.1002/aic.690100112).
 - Sutton, R. P. 2007. Fundamental PVT Calculations for Associated and Gas/Condensate Natural Gas Systems. Paper presented at the SPE Annual Technical Conference and Exhibition, Dallas, Texas, USA, October 2005. SPE-97099-MS. [https://doi.org/10.2118/97099-MS](https://doi.org/10.2118/97099-MS).