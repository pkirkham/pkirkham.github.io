---
layout: article
title: Fluid Properties and Equation of State
modified: 2026-01-08
categories: analysis
excerpt: Properties required for reservoir simulation and the basis for the cubic equation of state applied to predict them.
tags: [static_modelling, fluid_properties, density, formation_volume_factor, viscosity, equation_of_state, ppr78, pvt, eos]
image:
  feature:
  teaser: teaser-fluid-properties-400x250.png
  thumb:
comments: true
mathjax: true
---

Hydrocarbon fluids are mixtures comprising a number of different components. Depending on the environmental conditions that the fluid is subject to, that fluid can be either in a gaseous or liquid state, or comprising both liquid and vapour phases each of which has a different composition. As the fluid phase state, pressure and temperature change, the properties of that fluid also change. Understanding the relationship between the fluid phases and its properties in response to changes in pressure, volume and temperature (PVT) is a crucial activity when building a reservoir simulation.

In a simulator we generally want to know three different properties:

 - **Density:** The mass of a fluid relative to its volume. Fluid density (ρ) governs the pressure profile within a reservoir, including capillary pressures which in turn control the fluid saturations.
 - **Formation Volume Factor:** Also known as FVF this is the relationship between the volume of a fluid at reservoir conditions in comparison to standard conditions (~14.65 psia and 60 °F). This translates the volume of fluid contained within a reservoir pore volume into those at surface conditions which are typically used for measurement of sales volumes.
 - **Viscosity:** This governs how easily a fluid will flow. Viscosity (μ) is an input variable to Darcy's Law.

It is possible to calculate each of these properties for a fluid at every time step and for each cell in a reservoir. Indeed this is how a fully compositional simulator operates, with the fluid properties for at each time-step and in each reservoir cell determined according to a 'flash' calculation. In a modified black-oil simulation approach, given that many cells will have similar pressure and temperature for a consistent composition, the variation in properties can be expected to be linear and smooth as pressure varies. This means that usually the fluid property values are pre-calculated and provided to the simulator in the form of PVT look-up tables (LUT) over the pressure range of interest at reservoir temperature. In addition, the gas density at standard conditions must be supplied in order to calculate the gas FVF at any pressure.

For calculation of PVT tables we can either use correlations or an equation of state. Whilst correlations are useful for quick hand calculations, with the power of a computer available it makes more sense to use the more involved equation of state approach as this allows properties of arbitrary mixtures to be determined with relative ease.

## Reservoir Fluid Types

Fluids fall into three different classes. These are incompressible fluids, slightly compressible fluids and compressible fluids. The behaviour of each of these types of fluids is different in a simulator.

### Incompressible Fluid

This is an idealised fluid that can be used to represent dead oil (e.g. no solution gas) and water. An incompressible fluid has zero compressibility. A consequence of this is that irrespective of pressure, the density, FVF and viscosity are constant. Mathematically:

$$\rho\neq\mathrm{f}\left(P\right)=\mathrm{constant}$$

$$B\neq\mathrm{f}\left(P\right)=B^{o}\cong1$$

$$\mu\neq\mathrm{f}\left(P\right)=\mathrm{constant}$$

### Slightly Compressible Fluid

A slightly compressible fluid has a small but constant compressibility (c)that usually ranges from 10<sup>−5</sup> to 10<sup>−6</sup> psi<sup>−1</sup>. Gas-free oil, water and oil above bubble-point pressure are examples of slightly compressible fluids. The pressure dependence of the density, FVF and viscosity are expressed as:

$$\rho=\rho^{o}\left[1+c\left(P-P^{o}\right)\right]$$

$$B=\frac{B^{o}}{\left[1+c\left(P-P^{o}\right)\right]}$$

$$\mu=\frac{\mu^{o}}{\left[1+c_{\mu}\left(P-P^{o}\right)\right]}$$

$$c_{\mu}=-\frac{1}{\mu}\left(\frac{d\mu}{dP}\right)$$

Where:

ρ<sub>o</sub> = Fluid density at reference pressure (P<sub>o</sub>) and reservoir temperature, lbm/ft<sup>3</sup><br>
B<sub>o</sub> = FVF at reference pressure (P<sub>o</sub>) and reservoir temperature, rb/stb<br>
μ<sub>o</sub> = Viscosity at reference pressure (P<sub>o</sub>) and reservoir temperature, cp<br>
c<sub>μ</sub> = The fractional change of viscosity with pressure change (viscosibility)

Oil above its bubble-point pressure can be treated as slightly compressible fluid with the reference pressure being the oil bubble-point pressure and, in this case, ρ<sub>o</sub>, B<sub>o</sub>, and μ<sub>o</sub> are obtained from oil-saturated properties at the oil bubble-point pressure.

### Compressible Fluid

A compressible fluid has orders of magnitude higher compressibility than that of a slightly compressible fluid, usually 10<sup>−2</sup> to 10<sup>−4</sup> psi<sup>−1</sup> depending on pressure. The density and viscosity of a compressible fluid increase as pressure increases but tend to level off at high pressures. The FVF decreases orders of magnitude as the pressure increases from atmospheric pressure to high pressure. Natural gas is a good example of a compressible fluid. The pressure dependencies of the density, FVF and viscosity of natural gas are expressed as:

$$\rho_{g}=\frac{\rho{M}}{zRT}$$

$$B_{g}=\frac{\rho_{sc}}{\alpha_{c}\rho_{g}}=\frac{P_{sc}}{\alpha_{c}T_{sc}}\cdot{\frac{zT}{P}}$$

$$\mu_{g}=\mathrm{f}\left(T, P, M\right)$$

The volume conversion factor is α<sub>c</sub> = 5.6145835124493 which is the number of cubic feet in one barrel.

B<sub>g</sub> is usually stated in units of rb/Mscf.

Alternatively, the gas expansion factor (GEF) can be used. This uses the more intuitive units scf/rcf (or sm<sup>3</sup>/m<sup>3</sup>) and is defined as 1 / α<sub>c</sub>·B<sub>g</sub>.

## Equation of State

Fluid samples obtained from the reservoir can have their properties measured and analysed in a laboratory. Acquiring fluid samples, especially downhole samples which are more representative of the actual unaltered reservoir fluid, can be both technically challenging and expensive. Even where fluid samples are available, it is not unusual to discover that different samples obtained from the same part of the reservoir can exhibit different properties. Sometimes no laboratory measurement of properties is available, but a compositional analysis obtained using chromatography has been performed.

Using the composition and knowledge of chemistry and physics, the properties for a given fluid under specific conditions can be predicted using an [equation of state](https://en.wikipedia.org/wiki/Equation_of_state) (EoS). This is an algorithm that predicts for a given pressure and temperature what the associated phase and volume for the liquid and vapour compositions that make up the original fluid are.

The first progress towards understanding the behaviour of fluids was [Boyle's Law](https://en.wikipedia.org/wiki/Boyle%27s_law) as early as 1662 and is recognised as perhaps the earliest basic form of an equation of state. This established that the volume of a gas is inversely proportional to its pressure. The next advance came over 100 years later in 1787 with [Charles' Law](https://en.wikipedia.org/wiki/Charles%27s_law) which states the volume of a gas is proportional to its temperature under conditions of constant pressure (isobaric).

In 1934 Clapeyron was the first to combine both these laws into a single relationship, the ideal gas law, via the universal gas constant. For an ideal gas the pressure, volume and temperature are related to each other by the ideal gas law. At constant temperature, the pressure multiplied by the volume of the gas should remain constant, and for a constant pressure, as temperature increases the volume of the gas should expand.

$$PV=nRT$$

Where:

P = pressure<br>
V = volume<br>
T = temperature<br>
n = amount of substance, mol<br>
R = universal gas constant

For a constant temperature, the value nRT is constant and thus the equation indicates that pressure is inversely proportional to the volume. This is Boyle's Law. Similarly, for a constant pressure the value nR/P is a constant and volume is proportional to temperature. This is Charles' Law.

In reality, ideal gases do not exist although for many gas mixtures this behaviour can be assumed up to pressure of about 4 bar (60 psia). As pressure increases above this level the accuracy of the relationship decreases. Therefore, for real gases a correction factor z, also referred to as the gas-deviation factor, is introduced:

$$PV=znRT$$

### Cubic EoS

To model gas behaviour the use of a cubic equation of state is commonly applied. In the cubic EoS the gas deviation factor is solved by an equation of the form:

$$z^3+A_{2}z^2+A_{1}z+A_{0}=0$$

The constants A<sub>0</sub>, A<sub>1</sub>, A<sub>2</sub> are functions of pressure, temperature, and phase composition.

The first cubic EoS was proposed by van der Waals in 1873. This modifies the real gas equation as follows:
{% raw %}
$$(P+\frac{a}{{V_m}^2})(V_m-b)=RT$$
{% endraw %}
Where:

V<sub>m</sub> = Molar volume, the volume of one mole of gas or liquid = V / n

The modifications to the ideal gas law are two-fold and address the neglect of molecular size and intermolecular forces in the ideal gas law:

 1. It is conceptualised that molecules occupy a small volume and are not infinitessimal small points. Therefore there is an adjustment 'b' to the volume applied to subtract the space occupied by the molecules themselves. This parameter is often referred to as the co-volume and is included in a “repulsion” term since it reflects a physical reasoning that molecules will repulse each other if they are too close -- this is the behaviour of an incompressible liquid.
 2. Unlike in an ideal gas, molecules within a real gas will exert an attractive force on each other. This phenomenon manifests itself as a reduction in the pressure on the outer surface of a gas volume. Thus the effective pressure applicable to the ideal gas law is is increased by adding an "attractive" term = a / V<sub>m</sub><sup>2</sup> where 'a' is referred to as the energetic parameter.

For both the repulsive and attractive modifications, at high temperature and low pressure, where molecules in the gas are further apart and thus the gas has a large molecular volume, we note that (V<sub>m</sub> - b) ≈ V<sub>m</sub> since V<sub>m</sub> >> b, and (a / V<sub>m</sub><sup>2</sup>) ≈ 0. Therefore the equation reduces to the ideal gas law.

Re-arranging gives the form:

$$P=\frac{RT}{V_m-b}-\frac{a}{V_m^2}$$

In this form of the equation, the first term improves prediction of liquid behaviour since the volume approaches a limiting value of b at high pressures. This term represents the repulsive component of pressure on a molecular scale. The second term accounts for the non-ideal behaviour of the gas and is traditionally interpreted as the attractive component of pressure.

The energetic parameter 'a' in the attractive term and the co-volume 'b' in the repulsion term are defined as:

$$a=3p_c{V_c}^2$$

$$b=V_c/3$$

Using reduced state variables, V<sub>r</sub> = V<sub>m</sub> / V<sub>c</sub>, P<sub>r</sub> = P / P<sub>c</sub> and T<sub>r</sub> = T / T<sub>c</sub>, the van der Waals equation can be written:

$${V_r}^3-\left({\frac{1}{3}}+{\frac{8T_r}{3P_r}}\right){V_r}^2+{\frac{3V_r}{P_r}}-\frac{1}{P_r}=0$$

This is a cubic equation which has three roots. The largest and the lowest roots are the gas and liquid reduced volumes.

Almost all other cubic EoS follow a similar form to the original van der Waals equation and conceptually they function in a similar manner. The van der Waals equation is no longer used as other cubic EoS are more accurate, but the principles upon which the equation was developed are still relevant and worth understanding. So important were the concepts introduced that [Johannes Diderik van der Waals was awarded the Nobel prize in Physics 1910](https://www.nobelprize.org/prizes/physics/1910/summary/). 

### Peng-Robinson EoS

There have been many variations proposed and modifications to the basic cubic EoS. Redlich and Kwong (RK), working for Shell, introduced a temperature dependence to the attractive parameter 'a' in 1949. After the acentric factor was proposed by Pitzer in 1955 to account for the non-sphericity of molecules and was shown to be related to vapour pressure, it wasn't until 1972 that Soave modified the RK equation to introduce the [acentric factor](https://en.wikipedia.org/wiki/Acentric_factor) so that the attractive parameter 'a' is a function of both temperature and acentric factor. This gives the Soave-Redlich-Kwong (SRK) form for the cubic equation. The ability of the SRK equation to predict fluid properties led to its popular adoption during a period when computers were also becoming more widespread and which greatly improved the practicality of the equation's usage.

A recognised shortcoming of the SRK equation is that the critical compressibility factor Z<sub>c</sub> is a constant 0.333 for all substances. This does not match the experimental observations that suggest the critical compressibility factor for different substances is not a constant, and is close to an average of 0.27. The consequence is that molar volumes are typically overestimated, and thus densities are underestimated.

Shortly after in 1976, Peng and Robinson (PR) proposed their EOS as a result of a study sponsored by the Canadian Gas Commission. The objective of this EoS was to improve the predictive performance for natural gas systems. The inclusion of a temperature dependence and acentric factor found in SRK is maintained. For PR, the critical compressibility factor Z<sub>c</sub> is a constant 0.301 for all substances, which is an improvement over SRK and is closer to the experimentally measured values. Thus PR performs best of any EoS close to critical conditions and is widely used throughout the petroleum industry.

The classical form of the Peng-Robinson equation of state is:

$$P=\frac{RT}{V-b}-\frac{a}{V\left(V+b\right)+b\left(V-b\right)}$$

The EoS constants are given by:

$$a=a_{c}\cdot{\alpha}$$

$$a_c=\Omega_a\cdot\frac{R^2{T_c}^2}{P_c}$$

$$b=\Omega_b\cdot\frac{R{T_c}}{P_c}$$

Where:

&Omega;<sub>a</sub> ≈ 0.457235528921382<br>
&Omega;<sub>b</sub> ≈ 0.0777960739038884<br>
a<sub>c</sub> = Energetic parameter 'a' at critical temperature<br>
&alpha; = Temperature dependent &alpha;-function.

$$\alpha=\left[1+m\left(1-\sqrt{T_r}\right)\right]^2$$

$$m=0.37464+1.54226\omega-0.26992\omega^2$$

Or for heavier components with (ω > 0.49):

$$m=0.379642+1.48503\omega-0.164423\omega^2+0.016667\omega^3$$

The cubic equation is solved by combining the a and b parameters for each component. The cubic equation in terms of Z factor is given by:

$$z^3+(B-1)z^2+(A-3B^2-2B)z+(B^3+B^2-AB)=0$$

One or three roots may exist. The smallest positive root (assuming it is greater than B) is typically chosen for liquids and the largest root is chosen for vapours. The middle root is always discarded as a non-physical value.
To determine the parameters in the cubic equation, quadratic mixing rule for all components in the mixture is used for A and a linear rule for B.

$$A=\sum_{i=1}^{N}\sum_{j=1}^{N}z_iz_jA_{ij}$$

$$A_{ij}=(1-k_{ij})\sqrt{a_ia_j}$$

$$B=\sum_{i=1}^{N}z_ib_i$$

Where:

a<sub>i</sub> = Energetic parameter ‘a’ for component ‘i’<br>
b<sub>i</sub> = Co-volume ‘b’ for component ‘i’<br>
z<sub>i</sub> = Molar fraction for component ‘i’ in mixture<br>
k<sub>ij</sub> = Binary interaction parameter (BIP) where k<sub>ii</sub> = 0 and k<sub>ij</sub> = k<sub>ji</sub>

Many small incremental improvements or [variations on PR have been proposed in the recent decades](http://dx.doi.org/10.1016/j.fluid.2017.05.007) since the PR EoS was introduced, many of which seek to improve the calculation of the attractive 'a' parameter through modification of the temperature-dependent α parameter. This is usually done by changing the fit used for the acentric factor. An example is the Peng–Robinson-Stryjek-Vera EoS published in 1986. Others have sought to improve the volumetric predictive capability of the EoS through introduction of a volume shift to improve predicted volumes without affecting the equilibrium predictive capability of the EoS.

For the Pyrus approach a modification to the Peng-Robinson (1976) approach has been adopted which adjusts the binary interaction parameters based on a group contribution approach. This is referred to as the [Predictive Peng-Robinson (1978) EoS or PPR78](https://doi.org/10.1051/jeep/201100011).

### Predictive Peng-Robinson EoS

The [binary interaction parameters between pure components are set using the group contribution method outlined by Jaubert and Mutelet (2004)](https://www.researchgate.net/publication/232396448_VLE_Predictions_With_the_Peng-Robinson_Equation_of_State_and_Temperature_Dependent_kij_Calculated_Through_a_Group_Contribution_Method#read). This approach assigns physical meaning to the interaction parameters depending on the types of molecular groups that are present in the component. The k<sub>ij</sub> binary interaction parameters are temperature dependent and are predicted using the decomposition of the mixture into different molecular groups that make up each component in the composition. The group components that are implemented in this EOS are:

 - **-CH<sub>3</sub>:** Methyl group comprising one carbon atom bonded to three hydrogen atoms as part of a paraffinic chain.
 - **-CH<sub>2</sub>-:** Methylene group comprising one carbon atom bonded to two hydrogen atoms plus two single bonds as part of a paraffinic chain.
 - **>CH-:** Carbon-hydrogen group comprising one carbon atom bonded to one hydrogen atom plus three single bonds as part of a paraffinic chain.
 - **>C<:** Single carbon atom group with four single bonds as part of a paraffinic chain.
 - **CH<sub>4</sub>:** Methane group comprising one carbon atom bonded to four hydrogen atoms.
 - **C<sub>2</sub>H<sub>6</sub>:** Ethane group comprising two methyl groups bonded together.
 - **)>CH (R<sub>aro</sub>):** Carbon-hydrogen group comprising one carbon atom bonded to one hydrogen atom as part of an aromatic ring.
 - **)>C- (R<sub>aro</sub>):** Single carbon atom group as part of an aromatic ring plus a single bond.
 - **)>C<( (R<sub>fused</sub>):** Fused carbon atom group as part of fused aromatic (polyaromatic) rings.
 - **>CH<sub>2</sub> (R<sub>cyc</sub>):** Methylene group comprising one carbon atom bonded to two hydrogen atoms as part of a cyclic ring plus two single bonds.
 - **>CH- (R<sub>cyc</sub>):** Carbon-hydrogen group comprising one carbon atom bonded to one hydrogen atom as part of a cyclic ring plus a single bond.
 - **>C< (R<sub>cyc</sub>):** Single carbon atom group as part of a cyclic ring plus two single bonds.
 - **CO<sub>2</sub>:** Carbon dioxide group comprising a single carbon atom bonded to two oxygen atoms.
 - **N<sub>2</sub>:** Nitrogen group comprising two nitrogen atoms bonded together.
 - **H<sub>2</sub>S:** Hydrogen sulphide group comprising a single hydrogen atom bonded to two sulphur atoms.

For defined compounds the breakdown of a component into its groups is straightforward. For example, the paraffin nC4 (n-butane) with the chemical composition C<sub>4</sub>H<sub>10</sub> is represented as:

<div style="width: 800px; height: 300px; overflow: hidden">
<iframe style="width: 800px; height: 1200px; position: relative; top: -450px" frameborder="0" src="https://embed.molview.org/v1/?mode=balls&cid=7843&bg=white"></iframe>
</div>

This can be broken down into the groups: **CH3 – CH2 – CH2 – CH3**

Which is 2× ‘**-CH3**’ groups and 2× ‘**>CH2**’ groups.

Once the fractional contribution of each group to every component in the mixture is known, the binary interaction parameter is calculated using the following equation:

$$k_{ij}=\frac{-\frac{1}{2}\left[\sum_{k=1}^{N_g}\sum_{l=1}^{N_g}(\alpha_{ik}-\alpha_{jk})(\alpha_{il}-\alpha_{jl})A_{kl}\cdot{(\frac{298.15}{T})^{(\frac{B_{kl}}{A_{kl}}-1)}}\right]-\left(\frac{\sqrt{a_i}}{b_i}-\frac{\sqrt{a_j}}{b_j} \right)^2}{2\frac{\sqrt{a_i\cdot{a_j}}}{b_i\cdot{b_j}}}$$

Where:

α<sub>ik</sub> = fraction of molecule ‘i’ occupied by group ‘k’<br>
A<sub>kl</sub> = constant parameter determined by Jaubert and Mutelet between groups ‘k’ and ‘l’ where A<sub>kk</sub> = 0 and A<sub>kl</sub> = A<sub>lk</sub><br>
B<sub>kl</sub> = constant parameter determined by Jaubert and Mutelet between groups ‘k’ and ‘l’ where B<sub>kk</sub> = 0 and B<sub>kl</sub> = B<sub>lk</sub>

BIPs between the largest plus fraction and single carbon number components between one (methane) and six (hexane) are determined using the procedure recommended by Petersen, Thomassen and Fredenslund (1989). This is more critical where there is a single plus fraction such as C7+ and is less significant where a full composition is utilised.

### Translated-Consistent Peng-Robinson EoS

Guennec, Privat and Jaubert (2016) developed a translated-consistent cubic equation of state. This involves two key steps:

1.  **Consistent** &alpha;-function that passes a consistency test to guarantee safe extrapolation in the supercritical region and safe vapour-liquid equilibrium calculation in multi-component systems.
2. Volume **translation** where the experimental saturated liquid volume at a reduced temperature of 0.8 is matched.

It has been observed that &alpha;-functions strongly affect the accuracy of any cubic EoS. The choice of function not only affects the properties of pure components in the supercritical region, but the vapour-liquid equilibrium calculation of multi-component systems is also affected.

Many of the published &alpha;-functions, many of which are implemented in process simulation software programs, were found to fail the proposed consistency test. The &alpha;-function published by Twu (1991) was found to exhibit the best compromise between complexity and flexibility, and a consistent set of parameters were determined for hundreds of pure components. These [parameters were subsequently updated by Pina-Martinez et al. in 2018](https://doi.org/10.1021/acs.jced.8b00640) and the data for 1,721 molecules were determined.

The Twu &alpha;-function is given by:

$$\alpha={T_r}^{N(M-1)}\exp\left[L\left(1-{T_r}^{MN}\right)\right]$$

Where a specific &alpha;-function is not available, a generalised two-parameter equation can be used by setting N = 2. The Twu equation reduces to:

$$\alpha={T_r}^{2(M-1)}\exp\left[L\left(1-{T_r}^{2M}\right)\right]$$

The L and M parameters are then determined based on acentric factor alone as follows:

$$L=0.129\omega^2+0.6039\omega+0.0877$$

$$M=0.176\omega^2+0.26\omega+0.8884$$

Alternatively a generalised update to the Soave &alpha;-function in the Peng-Robinson EoS is proposed by [Pina-Martinez, Privat, Jaubert and Peng (2019)](https://doi.org/10.1016/j.fluid.2018.12.007) as follows:

$$m=0.3919+1.4996\omega-0.2721\omega^2+0.1063\omega^3$$

This generalised &alpha;-function is more accurate the the original for very heavy molecules (acentric factor ω is larger than 0.9).

#### Volume Translation

The equation of state must be solved iteratively to determine the liquid and vapour phase fractions for each component such that the fugacity of each phase is equal. At this point equilibrium is obtained. This ‘flash’ calculation is computationally intensive and its details are not described here. Once equilibrium has been determined, the mole fractions that make up the composition of the liquid and vapour phases can be determined as x<sub>i</sub> for the liquid phase and y<sub>i</sub> for the vapour phase.

The final step is the volume translation or shift. Peneloux (1982) showed that the volume shift approach does not affect equilibrium calculations for pure components or mixtures and therefore does not affect the vapour-liquid equilibrium functionality of the EoS. Volume translation solves the main problem with a two-constant EOS which is poor liquid volumetric predictions. The volume shift is applied using a simple correction term to the EOS-calculated molar volume.

$$\nu_L={\nu_L}^{EOS}-\sum_{i=1}^{N}x_ic_i$$

$$\nu_V={\nu_V}^{EOS}-\sum_{i=1}^{N}y_ic_i$$

The shift is temperature independent provided the reduced temperature is below 0.9. Using the consistent &alpha;-function, Guennec et al. were able to determine the volume translation parameter at a reduced temperature of 0.8 by comparing the EoS value against the experimentally measured value. The values were updated by Pina-Martinez et al. for 1,489 molecules. It should be noted that the use of these volume shift values is coupled to the choice of &alpha;-function, such that the molar volume should exactly match the experimental saturated liquid volume.

Where no values are published (for example with pseudo-components) then the volume shift can be determined using the approach of Jhaveri and Youngren (1988) where the shift is calculated in terms of the molecular weight and the paraffinicity of the component.

$$s_i=\frac{c_i}{b_i}=1-A_0/{M_i}^{A_1}$$

Where A<sub>0</sub> and A<sub>1</sub> are constants that vary depending on the paraffin, naphthene and aromatic fraction of the component. This volume shift is not coupled to the consistent &alpha;-function.

Guennec et al. also propose a generalised correlation for the volume shift based on the acentric factor. However, the accuracy of this correlation is not much better than the untranslated EoS, so it has not been considered further.

#### Modification to Binary Interaction Parameters

The accuracy of any cubic EoS is affected by both a combination of the choice of Soave &alpha;-function and the choice of mixing rules.

The PPR78 binary interaction parameters were determined using the &alpha;-function of the classical Peng-Robinson EoS. Therefore, as noted by [Pina-Martinez, Privat, Jaubert and Peng (2019)](https://doi.org/10.1016/j.fluid.2018.12.007) use of translated-consistent values requires that the BIPs are adjusted to correspond to the consistent &alpha;-function that is used. This is because the value of k<sub>ij</sub> is specific to the the choice of &alpha;-function.

$$k_{ij}^{updated}=\frac{2k_{ij}^{original}\delta_{i}^{original}\delta_{j}^{original}+(\delta_{i}^{original}-\delta_{j}^{original})^2-(\delta_{i}^{updated}-\delta_{j}^{updated})^2}{2\delta_{i}^{updated}\delta_{j}^{updated}}$$

Where:

$$\delta_{i}^{original}=\frac{\sqrt{a_{c,i}\cdot\alpha_{i}^{original}}}{b_i}$$

$$\delta_{i}^{updated}=\frac{\sqrt{a_{c,i}\cdot\alpha_{i}^{updated}}}{b_i}$$

Pina-Martinez et al. note that when they tested the effect on k<sub>ij</sub> for several binary systems, the change in value was small between the original and updated correlation. Thus the use of existing k<sub>ij</sub> values associated with the classical &alpha;-function for Peng-Robinson EoS could continue to be used although the authors note that updating the values is advised.

## References

 - Jaubert, J. N. and Mutelet, F. 2004. VLE Predictions with the Peng–Robinson Equation of State and Temperature Dependent kij Calculated through a Group Contribution Method. _Fluid Phase Equilibria_ **224** (2004): 285-304. [https://doi.org/10.1016/j.fluid.2004.06.059](https://doi.org/10.1016/j.fluid.2004.06.059).
 - Jaubert, J. N., Privat, R., and Mutelet, F. 2010. Predicting the Phase Equilibria of Synthetic Petroleum Fluids with the PPR78 Approach. _AIChE Journal_ **56** (12): 3225-3235. [https://doi.org/10.1002/aic.12232](https://doi.org/10.1002/aic.12232).
 - Jaubert, J. N., Qian, J. W., Lasala, S., and Privat, R. 2022. The Impressive Impact of Including Enthalpy and Heat Capacity of Mixing Data when Parameterising Equations of State. Application to the Development of the E-PPR78 (Enhanced-Predictive-Peng-Robinson-78) Model. _Fluid Phase Equilibria_ **560** (2022): 113456. [https://doi.org/10.1016/j.fluid.2022.113456](https://doi.org/10.1016/j.fluid.2022.113456).
 - Jaubert, J. N., Vitu, S., Mutelet, F., Corriou, J. P. 2005. Extension of the PPR78 Model (Predictive 1978, Peng–Robinson EOS with Temperature Dependent kij Calculated through a Group Contribution Method) to Systems Containing Aromatic Compounds. _Fluid Phase Equilibria_ **237** (2005): 193-211. [https://doi.org/10.1016/j.fluid.2005.09.003](https://doi.org/10.1016/j.fluid.2005.09.003).
 - Le Guennec, Y., Privat, R., Jaubert, J. N. 2016. Development of the Translated-consistent tc-PR and tc-RK Cubic Equations of State for a Safe and Accurate Prediction of Volumetric, Energetic and Saturation Properties of Pure Compounds in the Sub- and Super-critical Domains. _Fluid Phase Equilibria_ **428** (2016): 301-312. [https://doi.org/10.1016/j.fluid.2016.09.003](https://doi.org/10.1016/j.fluid.2016.09.003).
 - Lopez-Echeverry, J. S., Reif-Acherman, S., and Araujo-Lopez, E. 2017. Peng-Robinson Equation of State: 40 Years Through Cubics. _Fluid Phase Equilibria_ **447** (2017): 39-71. [https://doi.org/10.1016/j.fluid.2017.05.007](https://doi.org/10.1016/j.fluid.2017.05.007).
 - Peng, D. Y. and Robinson, D. B. 1976. A New Two-Constant Equation of State. _Ind Eng Chem Fundamen_ **15** (1): 59-64. [https://doi.org/10.1021/i160057a011](https://doi.org/10.1021/i160057a011).
 - Pina-Martinez, A., Le Guennec, Y., Privat, R., Jaubert, J. N., and Mathias, P. M. 2018. Analysis of the Combinations of Property Data That Are Suitable for a Safe Estimation of Consistent Twu &alpha;-Function Parameters: Updated Parameter Values for the Translated-Consistent tc-PR and tc-RK Cubic Equations of State. _J Chem Eng Data_ **63** (10): 3980-3988. [https://doi.org/10.1021/acs.jced.8b00640](https://doi.org/10.1021/acs.jced.8b00640).
 - Pina-Martinez, A., Privat, R., Jaubert, J. N., Peng, D. Y. 2019. Updated Versions of the Generalized Soave &alpha;-function Suitable for the Redlich-Kwong and Peng-Robinson Equations of State. _Fluid Phase Equilibria_ **485** (2019): 264-269. [https://doi.org/10.1016/j.fluid.2018.12.007](https://doi.org/10.1016/j.fluid.2018.12.007).
 - Privat, R. and Jaubert, J. N. 2011. PPR78, a Thermodynamic Model for the Prediction of Petroleum Fluid-phase Behaviour. Paper presented at the JEEP 37<sup>th</sup> Conference on Phase Equilibria, Saint-Avold, France, 30 March-1 April. [https://doi.org/10.1051/jeep/201100011](https://doi.org/10.1051/jeep/201100011).
 - Qian, J. W., Jaubert, J. N., and Privat, R. 2013. Prediction of the Phase Behavior of Alkene-containing Binary Systems with the PPR78 Model. _Fluid Phase Equilibria_ **354** (2013): 212-235. [https://doi.org/10.1016/j.fluid.2013.06.040](https://doi.org/10.1016/j.fluid.2013.06.040).
 - Qian, J. W., Privat, R., and Jaubert, J. N. 2013. Predicting the Phase Equilibria, Critical Phenomena, and Mixing Enthalpies of Binary Aqueous Systems Containing Alkanes, Cycloalkanes, Aromatics, Alkenes, and Gases (N<sub>2</sub>, CO<sub>2</sub>, H<sub>2</sub>S, H<sub>2</sub>) with the PPR78 Equation of State. _Ind Eng Chem Res_ **52** (46): 16457-16490. [https://doi.org/10.1021/ie402541h](https://doi.org/10.1021/ie402541h).
 - Vitu, S., Privat, R., Jaubert, J. N., Mutelet, F. 2008. Predicting the Phase Equilibria of CO<sub>2</sub> + Hydrocarbon Systems with the PPR78 model (PR EOS and kij Calculated through a Group Contribution Method). _J Supercritical Fluids_ **45** (2008): 1-26. [https://doi.org/10.1016/j.supflu.2007.11.015](https://doi.org/10.1016/j.supflu.2007.11.015).
 - Xu, X., Lasala, S., Privat, R., Jaubert, J. N. 2017. E-PPR78: A Proper Cubic EoS for Modelling Fluids Involved in the Design and Operation of Carbon Dioxide Capture and Storage (CCS) Processes. _Int J Greenhouse Gas Control_ **56** (2017): 126-154. [https://doi.org/10.1016/j.ijggc.2016.11.015](https://doi.org/10.1016/j.ijggc.2016.11.015).