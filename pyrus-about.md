---
layout: article
title: Pyrus Suite Of Oil And Gas Tools
tags: [pyrus]
permalink: /pyrus-about/
image:
  feature: feature-pyrus-1024x256.png
---

Originally I never intended to publish Pyrus as a software tool for others to use. Clearly the lack of an overall scope and vision from the early days hasn't helped in my belated goal to turn my code into something that can be shared with others.

Given the software is given away and shared freely, the ad-hoc and disorganised approach to its development can probably be forgiven? When I eventually piece most of it together and find myself in a position to charge for the final jigsaw piece, I'll clearly need to up my game.

## Background

Pyrus is a suite of oil and gas tools that has been under sporadic development since 1999 when I graduated from university. Much of the code has been used for my personal engineering work in the oil and gas industry, and it would be fair to say that much of the codebase was not even run on the command line, but from within the integrated development environment used to develop the code. For personal work this was good enough.

Inevitably there were always aspirations to pull a more complete package together and release something that others might find similarly useful. However, as I discovered in my first attempt to produce a standalone application in about 2010, there is a great deal involved in moving from something that mostly works to something that can be effectively used by another user. As my Dad told me, "the last 10% takes 90% of the effort".

At the end of 2023 I resumed attempts to produce a standalone desktop application. This was initially driven by a desire to produce some equation of state modelling tools for in-house use. Along the way I became intrigued as to whether a proper integrated development environment could be produced for the Eclipse reservoir simulation language -- and very specifically for the open-source [OPM Flow](https://opm-project.org/) flavour of that language. After all, how hard could it be?

To my own surprise, [within a month I had created a first pass workable solution]({{ site.url }}/pyrus/writing-editor-for-eclipse) which provides much greater utility than the conventional text editors that are used for preparing Eclipse input decks. This provided a renewed inspiration to make a proper effort to create something that could be used by other petroleum engineers, and led to the final (much overdue) publication of the initial Pyrus program.

### Genesis of The Code

Back in 1999, the initial code that would form the basis of Pyrus was written as a means to leverage code for [Seismic Uni*x](https://wiki.seg.org/wiki/Seismic_Unix) which was being run on the Sun Solaris platform for post-stack seismic processing of scanned and reconstructed 2D sections. This was a very steep learning curve that involved porting code from C and writing new code in Java, whilst learning the intricacies of byte-level file mainipulation needed for efficient reading and writing of SEG-Y files that were inevitably written in a variety of different encodings. At the time Java was a new language which offered the promise of "write once, run anywhere" as it would compile a high level-language into a machine-independent bytecode which was then executed on a virtual machine built for a specific platform. Provided a particular flavour had access to a Java virtual machine, it should be able to run a Java program irrespective of what platform it was developed on. I think I was still working on Windows 98 in that era, using Intel Pentium processors, but the machines running Seismic Un\*x were Sun machines running the [Solaris operating system](https://en.wikipedia.org/wiki/Oracle_Solaris) on [Sparc architecture](https://en.wikipedia.org/wiki/SPARC).

Anyway, this meant that [Java seemed like a good idea at the time, despite all the negativity it managed to garner](https://dzone.com/articles/the-good-and-the-bad-of-java-programming) (it's slow, the code is verbose and ugly, it doesn't run everywhere, the applications don't look like native programs, ... and so on). The choice meant that it was relatively easy to write the code on a Windows laptop, and run that same code on a Solaris desktop.

Over the years the various parts that I started off using, including the Java language, the NetBeans platform framework and various libraries providing useful support and functionality, have not only stood the test of time but have flourished. Other languages and frameworks have come in and out of fashion -- today Python seems to be popular, particularly with machine learning tasks. However, with such an established base of code, there is no compelling reason to switch simply for the sake of using the latest and greatest language.