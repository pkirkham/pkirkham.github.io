---
layout: archive
title: Pyrus Suite Of Oil And Gas Tools
permalink: /pyrus/
image:
  feature: feature-pyrus-1024x256.png
---

Pyrus is the [biological genus for the pear tree](https://en.wikipedia.org/wiki/Pear). So what? Well the initial incarnation of this suite of software tools was referred to as somewhat more cumbersome "Petroleum Economics And Resources" which has the acronym P.E.A.R. Rather than calling the software "Pear", the biological genus "Pyrus" sounds more sophisticated and just seemed like a better option.

## Download

The Pyrus software can simply be downloaded, unzipped to any destination and run from the appropriate executable in the `/bin` folder of the unzipped folder. For the Linux and MacOSX versions, it may be necessary to change the permissions on the shell file to be executable. Whilst this software is not open-source, it is made available free of charge as a courtesy to other engineers that may find it useful.

<a href="https://www.dropbox.com/s/d5zipke5254lsq2/pyrus_suite-win_x64.zip?dl=1" class="btn-inverse">Download `pyrus_suite-win_x64.zip` (18-Mar-2024 version for Windows x64 architecture)</a>

<a href="https://www.dropbox.com/s/spqcf5qyqvix9wg/pyrus_suite-linux_x64.zip?dl=1" class="btn-inverse">Download `pyrus_suite-linux_x64.zip` (18-Mar-2024 version for Linux x64 architecture)</a>

<a href="https://www.dropbox.com/s/o74pjvte51q9xty/pyrus_suite-macosx_x64.zip?dl=1" class="btn-inverse">Download `pyrus_suite-macosx_x64.zip` (18-Mar-2024 version for MacOSX x64 architecture)</a>

<a href="https://www.dropbox.com/s/iau0qt4dih0nom7/pyrus_suite-macosx_aarch64.zip?dl=1" class="btn-inverse">Download `pyrus_suite-macosx_aarch64.zip` (18-Mar-2024 version for MacOSX ARM architecture)</a>

## Features

There are only a few features implemented in the current version of the Pyrus Suite. It is anticipated that the number of tools will grow quickly as several of these have already been developed, but lack the necessary graphical user interface to be incorporated into a proper application.

### Implemented Tools

 - **Eclipse Language Editor:** An integrated development environment for Eclipse language reservoir simulation decks. A number of standard code editing features have already been implemented, including syntax highlighting, code completion, error highlighting and an in-built Eclipse language manual. Various keyboard shortcuts for text manipulation are also available. Work is underway on automatic code formatting, improvements to the error highlighting, code templating and integration with other aspects of the Pyrus suite e.g., automatic generation of PVT and VFP tables based on other Pyrus objects.
 - **Cubic Equation of State:** Cubic equations of state (EOS) are a computationally efficient way to model the pressure, volume and temperature (PVT) relationships for arbitrary compositions. In this respect they are much more powerful and useful in comparison to correlations. I am not aware of many free tools for modelling fluids using cubic equations of state. This tool implements the translated-consistent enhanced predictive Peng-Robinson EOS, which is a very recent and thorough implementation. Integration with the open-source CoolProp library has also been implemented, which provides properties for pure fluids such as carbon dioxide.

### Upcoming Tools

 - **Seismic Section Reconstruction:** This is the very first tool that was created. It is a port of earlier Fortran code, but with a greatly improved graphical user interface. The tool allows scanner or high-resolution photos of 2D seismic sections to be reconstructed in a digital SEG-Y format. This is more than just creating an image representation of the data, as the actual traces are reconstructed such that the frequencies are consistent with those of the original section.
 - **Monte Carlo Volumetrics:** Many years ago I had created a tool for running Monte Carlo simulations for estimation of prospect volumetrics. With each realisation, a production profile for the recoverable volume is also generated and run through a flexible economic fiscal model. The fiscal model can be set up to handle a wide range of Production Sharing Contract (PSC) and Tax-Royalty systems, and combinations thereof.
 - **Cost Estimation:** Estimating production facility costs depending on the processing functionality required, and well cost depending on the proposed trajectories, geological conditions and target bottom hole diameter.
 - **3D Geomodelling:** Integrating well trajectories, reservoir simulation grids and properties (static models) and 3D seismic into a unified 3D visualisation environment. There are many other software products that do this already, but they are typically very expensive. One aspect I was particularly interested in was how to better visualise the subsurface, taking advantage of the advances in 3D graphics. I was surprised to find that despite decades of history, the fundamental file formats used for 3D seismic were not well optimised for visualisation tasks. This led to a personal project where I tried to see if a better solution could be implemented, and what I found was that it absolutely could. I suspect that there are many other opportunities for improvement. However, a working 3D visualisation environment needs to be created first.
 - **Petrophysics:** Basic well log viewing and petrophysical analysis, including [estimation of porosity from drilling parameters](https://doi.org/10.2118/209811-PA).

## Requirements

The software is written in [Java](https://openjdk.org/) and is built on top of the [NetBeans rich client platform](https://netbeans.apache.org/tutorial/main/kb/docs/platform/). The program is offered as a platform-independent distribution that does not require installation. However, due to the tremendous variety of Java virtual machines that may or may not be installed in different environments, the latest Java runtime environment (JRE) is also bundled with the download and pre-configured so that no additional installation is required.

 - **OS Platform:** The software should run on a wide variety of platforms. Four versions have been pre-packaged for Windows, Linux, and MacOS (on x64 architecture e.g., Intel and AMD chips) and a separate MacOS distribution for the AArch64 architecture (ARM chips a.k.a. M1 etc.). With an suitable Java virtual machine, there is no reason why the software couldn't run on other combinations.
 - **CPU and Memory Hardware:** The Java Runtime Environment itself needs up to 4 GB of RAM, which is pretty paltry for modern machines. The software does take advantage of parallel processing for both maintaining a responsive graphical user interface, and for demanding computational loads. The software should automatically detect and utilise all the cores available to it.
 - **Graphics Hardware:** Whilst no 3D features are included in the current release, development using OpenGL is underway for 3D seismic visualisation and interpretation. This means that a range of graphics cards are supported.
 - **Storage Requirements:** Modern software has become increasingly bloated. The Pyrus
 - **Internet Connection:** Obviously an internet connection is needed to download the files. The software does not need an active internet connection to run. Indeed, it should be possible to run directly off a USB stick. If an internet connection is available, then the software can be set up to check for updates which can be downloaded and updated in-place without any need to re-download the full application distribution.

## Background

Pyrus is a suite of oil and gas tools that has been under sporadic development since 1999 when I graduated from university. Much of the code has been used for my personal engineering work in the oil and gas industry, and it would be fair to say that much of the codebase was not even run on the command line, but from within the integrated development environment used to develop the code. For personal work this was good enough.

Inevitably there were always aspirations to pull a more complete package together and release something that others might find similarly useful. However, as I discovered in my first attempt to produce a standalone application in about 2010, there is a great deal involved in moving from something that mostly works to something that can be effectively used by another user. As my Dad told me, "the last 10% takes 90% of the effort".

At the end of 2023 I resumed attempts to produce a standalone desktop application. This was initially driven by a desire to produce some equation of state modelling tools for in-house use. Along the way I became intrigued as to whether a proper integrated development environment could be produced for the Eclipse reservoir simulation language -- and very specifically for the open-source [OPM Flow](https://opm-project.org/) flavour of that language. Three months later I believe I have a workable solution which provides much greater utility than the conventional text editors that are used for preparing Eclipse input decks.

The initial code was written as a means to leverage code for [Seismic Uni*x](https://wiki.seg.org/wiki/Seismic_Unix) on the Sun Solaris platform for post-stack seismic processing of scanned and reconstructed 2D sections. This was a very steep learning curve that involved porting code from C and writing new code in Java, whilst learning the intricacies of byte-level file mainipulation needed for efficient reading and writing of SEG-Y files that were inevitably written in a variety of different encodings. At the time Java was a new language which offered the promise of "write once, run anywhere" as it would compile a high level-language into a machine-independent bytecode which was then executed on a virtual machine built for a specific platform. Provided a particular flavour had access to a Java virtual machine, it should be able to run a Java program irrespective of what platform it was developed on. I think I was still working on Windows 98 in that era, using Intel Pentium processors, but the machines running Seismic Un\*x were Sun machines running the [Solaris operating system](https://en.wikipedia.org/wiki/Oracle_Solaris) on [Sparc architecture](https://en.wikipedia.org/wiki/SPARC).

Anyway, this meant that Java seemed like a good idea at the time, despite all the negativity it managed to garner (it's slow, the code is verbose and ugly, it doesn't run everywhere, the applications don't look like native programs, ... and so on).

Over the years the various parts that I started off using, including the Java language, the NetBeans platform framework and various libraries providing useful support and functionality, have not only stood the test of time but have flourished. Other languages and frameworks have come in and out of fashion -- today Python seems to be popular, particularly with machine learning tasks. With such an established base of code, there is no compelling reason to switch.

## Documentation

There is a fledgling framework for a user guide built into the Pyrus Suite which can be accessed through the <kbd>F1</kbd> key. However, this is still being written. For the moment, users will just need to experiment and discover how the editor and equation of state tool work.

Articles related to the development of the software are collected here.

<div class="tiles">
{% for post in site.categories.pyrus %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->