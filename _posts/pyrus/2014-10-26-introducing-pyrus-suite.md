---
layout: article
title: Introducing The Pyrus Suite
modified:
categories: pyrus
excerpt: An oil and gas software suite created in Java for asset evaluation including volumetrics, material balance and economics.
tags: [pyrus_suite, java, netbeans, volumetrics, material_balance, economics, npv, seismic_scanning, modelling, software]
image:
  feature: feature-pyrus-1024x256.png
  teaser: teaser-pyrus-400x250.png
  thumb:
comments: true
---

The Pyrus Suite is a general purpose oil and gas asset evaluation tool. It has been sporadically developed over many years to meet a variety of analytical challenges that I have needed to solve in my job. In truth the software will never be 'complete' as it is under constant development, with progress largely determined by gaps in my work schedule.

## History Of The Pyrus Suite

Software development can be something of a roller-coaster ride and it is no different with the Pyrus Suite. There has been a massive transformation since the early versions were written, not just in the appearance of the software, but also in the direction that development is heading.

### The Code Is Born

I started coding the fore-runner of Pyrus in 1999 after I graduated from university. At the time there was never an intention for this work to grow into what it has become today. The initial efforts were directed primarily at trying to implement a post-stack implementation of a residual statics algorithm. Those of you familiar with geophysical processing will immediately recognise that residual statics is a pre-stack algorithm, so what on earth was I doing here?

Well my first 'job' was working with my father at New Wave Geophysical. New Wave is a seismic scanning business which takes scanned 2D paper seismic sections and recreates the digital SEG-Y that was used to print the section. Very useful when the original tapes are lost or deteriorated. Once the data has been recreated in digital form this opens it up to further processing options, but as 2D sections are post-stack data the number of algorithms that might be used is limited. Being young and naive I simply challenged my father on this. Surely there must be a way to do it, but no-one had ever really bothered before because why would you *need* to apply pre-stack algorithms to post-stack data, when you probably had the pre-stack data already?

You can imagine my astonishment when I came down to breakfast the next morning to find the dining table covered in paper with dad furiously scribbling out equations on them. It turned out that he had proven that you could apply residual statics to post-stack data *if* you knew the static corrections to start with. In other words it was possible to use a pre-stack algorithm on post-stack data. In principle. The problem was that there was no way to know what static corrections to apply, and there were a very large number of possible permutations.

Whilst at university I had heard about genetic algorithms from a classmate, and my interest was piqued. I explained to my father how they worked, and claimed that we would be able to use them to implement pre-stack routines on post-stack data. Actually I had no idea really, but it sounded like a good idea at the time. Despite some healthy scepticism I was given a green light to have a go at solving this conundrum, provided it didn't interfere with the other work I was doing for him at the time.

### Discovering Java

I now needed to write a genetic algorithm to implement all the equations that my father had written. Well I knew how to program as I'd been writing computer games in BASIC since I was a little kid. But I knew that BASIC was hardly a language for writing something as complex as a genetic algorithm. So what language to use? The choice was relatively easy. I ended up choosing Java simply because it was fashionable at the time, and because we were using Sun Solaris workstations and PCs. Being able to "write once, run anywhere", as the slogan went, was a real attraction.

My initial efforts were all based around writing code in a text editor, and compiling using the command line. Despite this very clunky approach, I was able to get a working implementation of the residual statics algorithm working. The progress would have been much slower without the code from the [Seismic Unix or SU](http://www.seismicunix.com/w/Main_Page) project to guide me. This code is written in C, but it was instrumental in helping me to understand how to read in and process seismic data. In doing so I learned a great deal about object-oriented programming and seismic processing. And in essence this was the start of the Pyrus Suite. A set of Java objects for seismic processing.

Once we had a working implementation for applying residual statics to the post-stack data, I could start to develop a genetic algorithm to determine what the static corrections to apply should be. This involved yet another steep learning curve as I had to teach myself about genetic algorithms. Fortunately they are not that hard to code, but what my father and I quickly discovered is that evolutionary strategy (essentially how do you determine 'fittest' in survival of the fittest) is not necessarily straightforward. At once point I thought that I should perhaps just code a brute force approach to see if it was even worth using a genetic algorithm, but when I calculated the processing time -- on the computers of the day -- it would have taken longer than the universe had been in existence. Eventually we managed to get the algorithm to produce a small improvement in the post-stack seismic, which we noted as a technical success. It is probably the first application of residual static corrections to post-stack data. Unfortunately the improvement in imaging quality was not substantial enough to actually sell as a service to clients, and we quietly dropped the development effort. 

### The NetBeans Rich Client Platform

Many years later my father and I were chatting about his business, and he lamented that one of the tasks that really needed to be done was to convert the software he uses so that it can on Windows, and not just on Solaris. Since the early days of my Java programming efforts, I had continued to develop small additions, mostly related to petroleum engineering which I needed for my work in the oil and gas industry. During this time I had graduated from text editors to using Forte for Java, and its successor the NetBeans integrated development environment (IDE). NetBeans itself is built on top of a framework which facilitates the windowing, and interoperability of various graphical user interface (GUI) components. This is known as the NetBeans Rich Client Platform (RCP). 

Whilst I had lost some of my youthful naivety, I was still ambitious. I knew I had a functioning set of tools that could be used to provide a framework for a front-end GUI for the seismic scanning software. We had the source code for the algorithms written in Fortran, and if there were any issues with understanding the logic, I could simply ask the man who devised the algorithms: my father. So I set to work on writing a graphical front-end to the Java code I had been updating for nearly a decade. In doing so Pyrus was born.

Soon after I was approached by [Geertjan Wielenga](https://blogs.oracle.com/geertjan/entry/welcome_to_me) to write an [article on my use of NetBeans RCP for the Pyrus Suite](https://dzone.com/articles/nb-petroleum-engineering). This is a nice record of how I used the NetBeans platform, and there are some early screenshots. Since that article development has continued, and most of the seismic scanning functionality has now been successfully ported over.

## The Future Of Pyrus

I will no doubt continue to develop Pyrus for many years to come. I believe the software has nearly reached a point where someone might actually find some aspects of it useful and be willing to pay for the software? Certainly a lot of effort has recently been expended towards making the software more robust and user-friendly.

There are two main avenues for development at present:

1. Completion of the Resurrect module for seismic scanning. Presently this is nearly finished, but there are many aspects that have yet to be written or tested. It is not ready for prime-time in its present state. The software has been tested by potential customers running trial versions, and the feedback received agreed with that view.
2. Develop a complete asset evaluation system from reservoir modelling through to economics. I have already developed a static volumetrics calculation module, and an economics module. What is missing is the piece in the middle that will tie the two together with dynamic modelling. Initially this could be as simple as a material balance simulation model. The ultimate goal is that any asset with just high level data could be modelled to determine reserves, production profiles, estimates of facilities and wells costs, opex, and all this run through an economic model. In addition the tool would use a stochastic simulation approach to realistically model the effects of resource uncertainty, and project uncertainty, on key outcomes such as cost growth, schedule slip, reserves deviation and production attainment. Unlike most stochastic simulations which just model these outcomes as independent events, the Pyrus approach would capture the fundamental links as it would be an integrated model from reservoir through to export capturing the key workflow from all disciplines.