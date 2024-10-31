---
layout: archive
title: Pyrus Suite Of Oil And Gas Tools
permalink: /pyrus/
image:
  feature: feature-pyrus-1024x256.png
---

Pyrus is my attempt to package a set of oil and gas technical and commercial tools that I have developed and used over several decades into a more user-friendly piece of software. There are a variety of tools that cover a range of difference disciplines, including several for which free software alternatives are hard to find or don't exist. The software is modular in nature, and only a few of the tools that I have developed are currently implemented in Pyrus. It is my ambition to continue to release new modules and functionality into the future. Whilst this software is not open-source, it is made available free of charge as a courtesy to other engineers who may find it useful.

Incidentally, the name Pyrus is the [biological genus for the pear tree](https://en.wikipedia.org/wiki/Pear). So what? Well the initial incarnation of this suite of software tools was referred to as somewhat more cumbersome "Petroleum Economics And Resources" which has the acronym P.E.A.R. Rather than calling the software "Pear", the biological genus "Pyrus" sounds more sophisticated and just seemed like a better option.

## Download

The Pyrus software can simply be downloaded, unzipped to any destination and run from the appropriate executable in the `/bin` folder of the unzipped folder. For the Linux and MacOS versions, it may be necessary to change the permissions on the shell file to be executable. The software will automatically update any installed modules as newer versions become available, and it is also possible to download and install new modular features.

<a href="https://bit.ly/Pyrus_Suite-Win_x64" class="btn-inverse">Download `pyrus_suite-win_x64.zip` (version for Windows x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-Linux_x64" class="btn-inverse">Download `pyrus_suite-linux_x64.zip` (version for Linux x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-MacOS_x64" class="btn-inverse">Download `pyrus_suite-macos_x64.zip` (version for MacOS x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-MacOS_aarch64" class="btn-inverse">Download `pyrus_suite-macos_aarch64.zip` (version for MacOS ARM architecture)</a>

## Features

During the initial development and roll-out of Pyrus there are only a few features implemented out of the backlog of code I've written (and started but not finished) over the decades. It is anticipated that the number of tools will grow quickly as several of these have already been developed, but just lack the necessary graphical user interface to be incorporated into a proper application. Help on features included in the current release can be accessed through the built-in help tool which is accessed using the <kbd>F1</kbd> key. The manual is a continual work-in-progress, and it is expected this will lag behind the features incorporated into Pyrus and the description of those features on this blog site.

### Implemented Tools

These functions have had a graphical user interface written for them, and can be used directly in Pyrus. Improvements will continue to be made to the underlying foundational code and the graphical user interfaces.

 - **[Eclipse Language Editor]({{ site.url }}/pyrus/eclipse-language-editor-features):** An integrated development environment for Eclipse language reservoir simulation decks. A number of standard code editing features have already been implemented, including syntax highlighting, code folding, code completion, code formatting, error highlighting and an in-built Eclipse language manual. Various keyboard shortcuts for text manipulation are also available. Work is underway on improvements to the error highlighting, code templating and integration with other aspects of the Pyrus suite.
 - **[Cubic Equation of State]({{ site.url }}/pyrus/pvt-fluid-properties-tool):** Cubic equations of state (EOS) are a computationally efficient way to model the pressure, volume and temperature (PVT) relationships for arbitrary compositions. In this respect they are much more powerful and useful in comparison to correlations. I am not aware of many free tools for modelling fluids using cubic equations of state. This tool implements the translated-consistent enhanced predictive Peng-Robinson EOS, which is a very recent and thorough implementation. Integration with the open-source CoolProp library has also been implemented, which provides properties for pure fluids such as carbon dioxide.
 - **[Rock Matrix Editor]({{ site.url }}/pyrus/rock-matrix-editor):** Pyrus uses a fundamental description of a rock matrix that encapsulates the lithology and pore geometry in order to predict reservoir engineering parameters. This functionality assists with translating geological descriptions into simulation input values. This allows creation of relative permeability curves, and porosity-permeability relationships, without routine or special core analysis.
 - **[Fluid Composition Estimator]({{ site.url }}/analysis/composition-creation/):** Allows an approximation of a hydrocarbon fluid composition to C7+ fraction to be obtained from field separator measurements alone. The algorithm is based on a novel genetic algorithm approach that can be combined with the Monte Carlo technique to find a reasonable approximation for a fluid composition that is consistent with the separator measurements.

### Upcoming Tools

These are tools for which code already exists, either completed and functional, or partially completed. All functions need to have a graphical user interface applied as the codebase is currently only accessible either through an application programming interface (API) or via the command line or equivalent.

 - **Seismic Section Reconstruction:** This is the very first tool that was created. It is a port of earlier Fortran code, but with a greatly improved graphical user interface. The tool allows scanner or high-resolution photos of 2D seismic sections to be reconstructed in a digital SEG-Y format. This is more than just creating an image representation of the data, as the actual traces are reconstructed such that the frequencies are consistent with those of the original section.
 - **Saturation Height Functions:** Modelled capillary pressure curves based on the porosity-permeability relationship allow saturation height functions to be generated. This is an additional feature to be incorporated into the rock matrix editor.
 - **Nodal Analysis:** Combined generation of vertical lift performance (VLP) curves and inflow performance relationship (IPR) for wells. For a defined wellbore geometry a range of VLP curves are generated that are compatible with the `VFPPROD` and `VFPINJ` keywords for ECLIPSE reservoir simulator. These curves take into consideration flow regimes, equation of state for fluids, and both pressure and temperature profiles in flowing wells. Semi-analytic IPR for vertical and horizontal wells is provided.
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

## Requirements

The software is written in [Java](https://openjdk.org/) and is built on top of the [NetBeans rich client platform](https://netbeans.apache.org/tutorial/main/kb/docs/platform/). The program is offered as a platform-independent distribution that does not require installation. However, due to the tremendous variety of Java virtual machines that may or may not be installed in different environments, the latest Java runtime environment (JRE) is also bundled with the download and pre-configured so that no additional installation is required.

 - **OS Platform:** The software should run on a wide variety of platforms. Four versions have been pre-packaged for Windows, Linux, and MacOS (on x64 architecture e.g., Intel and AMD chips) and a separate MacOS distribution for the AArch64 architecture (ARM chips a.k.a. M1 etc.). With an suitable Java virtual machine, there is no reason why the software couldn't run on other combinations.
 - **CPU and Memory Hardware:** The Java Runtime Environment itself needs up to 4 GB of RAM, which is pretty paltry for modern machines. The software does take advantage of parallel processing for both maintaining a responsive graphical user interface, and for demanding computational loads. The software should automatically detect and utilise all the cores available to it.
 - **Graphics Hardware:** Whilst no 3D features are included in the current release, development using OpenGL is underway for 3D seismic visualisation and interpretation. This means that when 3D features are implemented, a range of graphics cards are supported.
 - **Storage Requirements:** Modern software has become increasingly bloated and the Pyrus software is no exception. This is because in addition to the modular functionality that Pyrus creates, the software is built on top of many other libraries, not all portions of which are used by Pyrus. These libraries include the Java runtime environment that is distributed with the application, the NetBeans platform that provides the main framework, and open source utility libraries for image manipulation, data access, 3D graphics etc. In comparison to the size of the datasets commonly encountered in the oil and gas industry, the download size for the software is not large. Once uncompressed you will need ~0.6 GB of free storage space.
 - **Internet Connection:** Obviously an internet connection is needed to download the files. The software does not need an active internet connection to run. Indeed, it should be possible to run directly off a USB stick. If an internet connection is available, then the software can be set up to check for updates which can be downloaded and updated in-place without any need to re-download the full application distribution.

## Background

Pyrus is a suite of oil and gas tools that has been under sporadic development since 1999 when I graduated from university. Much of the code has been used for my personal engineering work in the oil and gas industry, and it would be fair to say that much of the codebase was not even run on the command line, but from within the integrated development environment used to develop the code. For personal work this was good enough.

Inevitably there were always aspirations to pull a more complete package together and release something that others might find similarly useful. However, as I discovered in my first attempt to produce a standalone application in about 2010, there is a great deal involved in moving from something that mostly works to something that can be effectively used by another user. As my Dad told me, "the last 10% takes 90% of the effort".

At the end of 2023 I resumed attempts to produce a standalone desktop application. This was initially driven by a desire to produce some equation of state modelling tools for in-house use. Along the way I became intrigued as to whether a proper integrated development environment could be produced for the Eclipse reservoir simulation language -- and very specifically for the open-source [OPM Flow](https://opm-project.org/) flavour of that language. After all, how hard could it be?

To my own surprise, [within a month I had created a first pass workable solution]({{ site.url }}/pyrus/writing-editor-for-eclipse) which provides much greater utility than the conventional text editors that are used for preparing Eclipse input decks. This provided a renewed inspiration to make a proper effort to create something that could be used by other petroleum engineers, and led to the final (much overdue) publication of the initial Pyrus program.

### Genesis of The Code

Back in 1999, the initial code that would form the basis of Pyrus was written as a means to leverage code for [Seismic Uni*x](https://wiki.seg.org/wiki/Seismic_Unix) which was being run on the Sun Solaris platform for post-stack seismic processing of scanned and reconstructed 2D sections. This was a very steep learning curve that involved porting code from C and writing new code in Java, whilst learning the intricacies of byte-level file mainipulation needed for efficient reading and writing of SEG-Y files that were inevitably written in a variety of different encodings. At the time Java was a new language which offered the promise of "write once, run anywhere" as it would compile a high level-language into a machine-independent bytecode which was then executed on a virtual machine built for a specific platform. Provided a particular flavour had access to a Java virtual machine, it should be able to run a Java program irrespective of what platform it was developed on. I think I was still working on Windows 98 in that era, using Intel Pentium processors, but the machines running Seismic Un\*x were Sun machines running the [Solaris operating system](https://en.wikipedia.org/wiki/Oracle_Solaris) on [Sparc architecture](https://en.wikipedia.org/wiki/SPARC).

Anyway, this meant that [Java seemed like a good idea at the time, despite all the negativity it managed to garner](https://dzone.com/articles/the-good-and-the-bad-of-java-programming) (it's slow, the code is verbose and ugly, it doesn't run everywhere, the applications don't look like native programs, ... and so on). The choice meant that it was relatively easy to write the code on a Windows laptop, and run that same code on a Solaris desktop.

Over the years the various parts that I started off using, including the Java language, the NetBeans platform framework and various libraries providing useful support and functionality, have not only stood the test of time but have flourished. Other languages and frameworks have come in and out of fashion -- today Python seems to be popular, particularly with machine learning tasks. However, with such an established base of code, there is no compelling reason to switch simply for the sake of using the latest and greatest language.

## Documentation

Articles related to the development of the software are collected here.

<div class="tiles">
{% for post in site.categories.pyrus %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->