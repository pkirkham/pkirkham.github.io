---
layout: article
title: Pyrus Suite Of Oil And Gas Tools
tags: [pyrus]
permalink: /pyrus-features/
image:
  feature: feature-pyrus-1024x256.png
---

As Pyrus is being developed, tools are being added as and when they are completed. A great deal of the code was written years ago, almost all of it for use from a command line environment. What is lacking for most of the tools is a graphical user interface (GUI). Surprisingly, this takes longer to develop than the core computational components!

The ultimate goal is for the software to assist with taking a limited oil and gas dataset, apply multi-discipline analysis to that data to build a reservoir model, and from that model generate an economic model.

## Features

During the initial development and roll-out of Pyrus there are only a few features implemented out of the backlog of code I've written (and started but not finished) over the decades. It is anticipated that the number of tools will grow quickly as several of these have already been developed, but just lack the necessary GUI to be incorporated into a proper application. Help on features included in the current release can be accessed through the built-in help tool which is accessed using the <kbd>F1</kbd> key. The manual is a continual work-in-progress, and it is expected this will lag behind the features incorporated into Pyrus and the description of those features on this blog site.

### Implemented Tools

These functions have had a graphical user interface written for them, and can be used directly in Pyrus. Improvements will continue to be made to the underlying foundational code and the graphical user interfaces.

 - **[Eclipse Language Editor]({{ site.url }}/pyrus/eclipse-language-editor-features):** An integrated development environment for Eclipse language reservoir simulation decks. A number of standard code editing features have already been implemented, including syntax highlighting, code folding, code completion, code formatting, error highlighting and an in-built Eclipse language manual. Various keyboard shortcuts for text manipulation are also available. Work is underway on improvements to the error highlighting, code templating and integration with other aspects of the Pyrus suite.
 - **[Cubic Equation of State]({{ site.url }}/pyrus/pvt-fluid-properties-tool):** Cubic equations of state (EOS) are a computationally efficient way to model the pressure, volume and temperature (PVT) relationships for arbitrary compositions. In this respect they are much more powerful and useful in comparison to correlations. I am not aware of many free tools for modelling fluids using cubic equations of state. This tool implements the translated-consistent enhanced predictive Peng-Robinson EOS, which is a very recent and thorough implementation. Integration with the open-source CoolProp library has also been implemented, which provides properties for pure fluids such as carbon dioxide.
 - **[Rock Matrix Editor]({{ site.url }}/pyrus/rock-matrix-editor):** Pyrus uses a fundamental description of a rock matrix that encapsulates the lithology and pore geometry in order to predict reservoir engineering parameters. This functionality assists with translating geological descriptions into simulation input values. This allows creation of relative permeability curves, and porosity-permeability relationships, without routine or special core analysis.
 - **[Fluid Composition Estimator]({{ site.url }}/analysis/composition-creation/):** Allows an approximation of a hydrocarbon fluid composition to C7+ fraction to be obtained from field separator measurements alone. The algorithm is based on a novel genetic algorithm approach that can be combined with the Monte Carlo technique to find a reasonable approximation for a fluid composition that is consistent with the separator measurements.
 - **Seismic Section Reconstruction:** This is the very first tool that was created. It is a port of earlier Fortran code, but with a greatly improved graphical user interface. The tool allows scanner or high-resolution photos of 2D seismic sections to be reconstructed in a digital SEG-Y format. This is more than just creating an image representation of the data, as the actual traces are reconstructed such that the frequencies are consistent with those of the original section.
 - **[Multiphase Well Flow]({{ site.url }}/analysis/simulating-well-flow-performance/):** Generation of vertical lift performance (VLP) curves for production and injection wells. For a defined wellbore geometry a range of VLP curves are generated that are compatible with the `VFPPROD` and `VFPINJ` keywords for ECLIPSE reservoir simulator. These curves take into consideration flow regimes, equation of state for fluids, and both pressure and temperature profiles in flowing wells.

### Upcoming Tools

These are tools for which code already exists, either completed and functional, or partially completed. All functions need to have a graphical user interface applied as the codebase is currently only accessible either through an application programming interface (API) or via the command line or equivalent.

 - **Saturation Height Functions:** Modelled capillary pressure curves based on the porosity-permeability relationship allow saturation height functions to be generated. This is an additional feature to be incorporated into the rock matrix editor.
 - **Nodal Analysis:** Combines generation of vertical lift performance (VLP) curves and inflow performance relationship (IPR) for wells to determine well production rates. Semi-analytic IPR for vertical and horizontal wells is provided.
 - **Monte Carlo Volumetrics:** Many years ago I had created a tool for running Monte Carlo simulations for estimation of prospect volumetrics. With each realisation, a production profile for the recoverable volume is also generated and run through a flexible economic fiscal model.
 - **Fiscal Modelling:** The fiscal model can be set up to handle a wide range of Production Sharing Contract (PSC) and Tax-Royalty systems, and combinations thereof. Creation of the fiscal model is graph-based, allowing the fiscal terms and cash flow streams to be described at a high level. The tool is capable of both calculating economics within Pyrus, and exporting an Excel (or other spreadsheet) compatible model.
 - **Drilling Time and Cost Estimation:** Based on known or expected geological environment, surface and bottomhole target locations, a well design is generated and the activities necessary to construct the well are incorporated into a detailed plan. From the plan a cost estimate is generated based on expected day rates for services and intangibles, and rates for consumables and other tangibles.
 - **Facility Cost Estimation:** Estimating production facility costs depending on the processing functionality required. Uses a factored estimation approach for topsides derived from a generated process flow diagram.
 - **3D Geomodelling:** Integrating well trajectories, reservoir simulation grids and properties (static models) and 3D seismic into a unified 3D visualisation environment. There are many other software products that do this already, but they are typically very expensive. One aspect I was particularly interested in was how to better visualise the subsurface, taking advantage of the advances in 3D graphics. I was surprised to find that despite decades of history, the fundamental file formats used for 3D seismic were not well optimised for visualisation tasks. This led to a personal project where [I tried to see if a better solution could be implemented]({{ site.url }}/pyrus/chunking-with-hdf5-format), and what I found was that it absolutely could. I suspect that there are many other opportunities for improvement. However, a working 3D visualisation environment needs to be created first.
 - **Petrophysics:** Basic well log viewing and petrophysical analysis, including [estimation of porosity from drilling parameters](https://doi.org/10.2118/209811-PA).

### Future Tools

These are tools which might be included in Pyrus at a future date, but for which no direct coding has been started (I've never had a need to create a tool to address these topics).

 - **Pressure Transient Analysis:** Analysis of well build-up tests.
 - **Decline Curve Analysis:** Analysis of well production data.