---
layout: article
title: Download Pyrus Suite Of Oil And Gas Tools
tags: [pyrus]
permalink: /pyrus-download/
image:
  feature: feature-pyrus-1024x256.png
---

A goal for Pyrus is that it must be able to run on almost any computer. Most modern approaches (as of 2025) to implement such a solution involve writing software that will run across a browser, leveraging what is known as [cloud compute](https://en.wikipedia.org/wiki/Cloud_computing). The idea is that the client machine's browser will render the user interface, with dynamic functionality implementing using a client-side language such as JavaScript, and the heavy number crunching is passed off to a remote server. When the results are available, they are passed back to the client machine which displays them. It is a similar model to the early days of computing with mainframes.

Perhaps it is bad memories of trying to write early cross-browser code for Netscape Navigator and Internet Explorer back in 1999 when dial-up modem speeds of 55.6 kbps were blazing fast. In those days I actually wrote my own modest graphic user interface for a simple image comparison tool. At the time there were no JavaScript libraries available, and this was very much cutting edge stuff. The downside was the bandwidth available at the time. It just made the whole user experience awful. These days things have improved, but as bandwidth increases, the cynic in me notes that websites just seem to cram more in so that the overall experience remains lacklustre.

I quite like the desktop. The data sits closer to the user. There is less latency. The challenge is how to make your software run on any computer? If you look closely at the year I was losing interest in writing web-based applications you'll note that this co-incides with the advent of Java and it's "write once, run anywhere" mantra. I was an early adopter of Java and haven't looked back since.

## Download

The Pyrus software can simply be downloaded, unzipped to any destination and run from the appropriate executable in the `/bin` folder of the unzipped folder. The software will automatically update any installed modules as newer versions become available, and it is also possible to download and install new modular features.

<a href="https://bit.ly/Pyrus_Suite-Win_x64" class="btn-inverse">Download `pyrus_suite-win_x64.zip` (version for Windows x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-Linux_x64" class="btn-inverse">Download `pyrus_suite-linux_x64.zip` (version for Linux x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-MacOS_x64" class="btn-inverse">Download `pyrus_suite-macos_x64.zip` (version for MacOS x64 architecture)</a>

<a href="https://bit.ly/Pyrus_Suite-MacOS_aarch64" class="btn-inverse">Download `pyrus_suite-macos_aarch64.zip` (version for MacOS ARM architecture)</a>

A licence is required for some features. Whilst Pyrus is under development a time-limited full licence can be freely downloaded. The licence can be installed from the menu `Tools > Licenses > Install License`.

<a href="https://www.dropbox.com/scl/fi/fjjx0z7z3j6seuwgsse1u/beta-license.lic?rlkey=9sth15g1c2geg9ndmbhn0smv0&dl=1" class="btn-inverse">Download `beta-license.lic`</a>

### Portable Distributions

The distributions provided here are referred to as "[portable](https://en.wikipedia.org/wiki/Portable_application)" distributions. This means that they won't require installation on a host operating system, and could, in theory, just be run from a USB drive mounted to the computer. This means that the software might appear unusual to users who are more familiar with the standard installation procedure used in some operating systems that creates handy menu items and desktop application launcher shortcuts. The benefit is that it can be used by users who don't have installation rights on the machine they are using (common in corporate environments).

To achieve this, the software includes a pre-packaged Java Runtime Environment (JRE). No separate installation of Java onto the host operating system should be required. The JRE provided is based on [OpenJDK](https://en.wikipedia.org/wiki/OpenJDK) which means there are no licencing issues to contend with.

## Requirements

The software is written in [Java](https://openjdk.org/) and is built on top of the [NetBeans rich client platform](https://netbeans.apache.org/tutorial/main/kb/docs/platform/). The program is offered as a platform-independent distribution that does not require installation. However, due to the tremendous variety of Java virtual machines that may or may not be installed in different environments, the latest Java runtime environment (JRE) is also bundled with the download and pre-configured so that no additional installation is required.

 - **OS Platform:** The software should run on a wide variety of platforms. Four versions have been pre-packaged for Windows, Linux, and MacOS (on x64 architecture e.g., Intel and AMD chips) and a separate MacOS distribution for the AArch64 architecture (ARM chips a.k.a. M1 etc.). With an suitable Java virtual machine, there is no reason why the software couldn't run on other combinations.
 - **CPU and Memory Hardware:** The Java Runtime Environment itself needs up to 4 GB of RAM, which is pretty paltry for modern machines. The software does take advantage of parallel processing for both maintaining a responsive graphical user interface, and for demanding computational loads. The software should automatically detect and utilise all the cores available to it.
 - **Graphics Hardware:** Whilst no 3D features are included in the current release, development using OpenGL is underway for 3D seismic visualisation and interpretation. This means that when 3D features are implemented, a range of graphics cards are supported.
 - **Storage Requirements:** Modern software has become increasingly bloated and the Pyrus software is no exception. This is because in addition to the modular functionality that Pyrus creates, the software is built on top of many other libraries, not all portions of which are used by Pyrus. These libraries include the Java runtime environment that is distributed with the application, the NetBeans platform that provides the main framework, and open source utility libraries for image manipulation, data access, 3D graphics etc. In comparison to the size of the datasets commonly encountered in the oil and gas industry, the download size for the software is not large. Once uncompressed you will need ~0.6 GB of free storage space.
 - **Internet Connection:** Obviously an internet connection is needed to download the files. The software does not need an active internet connection to run. Indeed, it should be possible to run directly off a USB stick. If an internet connection is available, then the software can be set up to check for updates which can be downloaded and updated in-place without any need to re-download the full application distribution.

## Known Issues

### Shell Script Won't Execute on Linux or MacOS

For the Linux and MacOS versions, it may be necessary to change the permissions on the shell file to be executable. This is accomplished with the terminal command:

`chmod +x pyrus_suite-linux_x64/bin/pyrus_suite`

`chmod +x pyrus_suite-macos_x64/bin/pyrus_suite`

`chmod +x pyrus_suite-macos_aarch64/bin/pyrus_suite`

Use the command applicable to the operating system you are using.

### Can't Find Java on MacOS

For the moment I don't have access to a Macintosh machine to test out deployment of Pyrus. Some helpful colleagues have tried installing on their machines to troubleshoot MacOS issues with Pyrus, and from this experience it appears that the bundled JRE is not recognised when running the portable application. If this occurs, a message will appear telling you that the application was unable to find Java.

To circumvent the problem, a brute force solution is simply to [download the latest JRE](https://www.azul.com/downloads/?os=macos&package=jre#zulu) and [install into MacOS](https://docs.azul.com/core/install/macos).