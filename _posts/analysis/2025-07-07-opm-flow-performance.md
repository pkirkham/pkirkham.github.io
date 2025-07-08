---
layout: article
title: OPM Flow Performance Improvements
modified:
categories: analysis
excerpt: Improvements in OPM Flow run times.
tags: [reservoir_engineering, simulation, software, OPM_Flow]
image:
  feature: feature-opm-flow-performance-1024x256.jpg
  teaser: teaser-opm-flow-performance-400x250.jpg
  thumb:
comments: false
mathjax: false
---

Reservoir simulation has been around for several decades now, and during this time it has moved from the domain of supercomputers to running on desktops and everyday laptops. As computing speeds have continued to improve, this has made it increasingly realistic to run modest simulations on a laptop rather than specialist hardware. Of course, just because you can do something, doesn't necessarily mean you should!

Over the past few years I've been running reservoir simulations on the Pasca A field in Papua New Guinea. Thanks to the late [David Baxendale](https://jpt.spe.org/memoriam-david-baxendale) we've been using the [OPM Flow](https://opm-project.org/) software which works well for simulation of modified black oil models using ECLIPSE format input decks.

Usually I've been running these simulations on decent desktop machine. Not quite a high-end desktop, but something approaching that. The main specification is a [Ryzen 9 5950X CPU with 16 cores and 64 GB of RAM]({{ site.url }}/blog/upgrading-the-x370/). This reduced run times for the Pasca A field noticeably in comparison to previous simulation runs around 10 years ago on quad-core machines. Using [Windows Subsystem for Linux (WSL) on Windows 11 with OPM Flow]({{ site.url }}/analysis/running-opm-flow-on-windows-subsystem-for-linux/), visualising the results in [ResInsight](https://resinsight.org/) had become normal and I was reluctant to change unless I was upgrading to a more powerful machine.

Last December the PNG government finally approved the Gas Agreement for the Pasca A field. This means that the project can move ahead towards development. Whilst it's great not to be waiting any longer, a practical consequence of this is that I have been travelling to and from Perth frequently. Along the way I've been issued with a new laptop. Nothing fancy mind you. A simple Intel i5 with a measly 8 GB RAM. I figured that if I needed to run a simulation it might allow me to get by in a pinch, but the desktop would still be the go-to machine.

Turns out that this was an incorrect assumption. Not because of the laptop's overwhelming power, but because of improvements that have been made to OPM Flow. Honestly, I'd never even seen any announcements about this, so it came as a suprise when I re-ran one of our standard simulation decks on the laptop to discover it completed faster than my desktop. Excuse me? Is this a mistake?

## Benchmarking OPM Flow

When benchmarking it helps to change a few elements as possible. Fortunately I have a specific gas-recycling scenario, which is useful because it is large enough to take nearly an hour to run (originally 45 minutes on my Ryzen 9 5950X desktop, over an hour on the machines we had at Twinza), but small and simple enough that it won't have multiple hours.

I was able to run this on the laptop after installing WSL and the latest version of OPM Flow, and was pleasantly surprised to find it took about 30 minutes. This is two-thirds the time taken on the desktop. Given that I had been expecting it to take a couple of hours, this was astonishing. Why was it so fast? Was this an amazing sleeper computer which performed well above what might be expected of its silicon?

As shown in Figure 1 below, it turns out that it wasn't the hardware at all. The performance is entirely down to OPM Flow improvements. The chart on the left shows the overall simulation time in green. The three points are the original run using OPM Flow version 2022.04-rc1 on an AMD Ryzen 9 5950X CPU with 64 GB RAM, the surprise run using OPM Flow version 2024.10 on an Intel i5 with 8 GB RAM, and finally a repeat/check using the latest OPM Flow version 2025.04 back on the AMD system. Evidently each of these is faster than the previous system.

The last two comparison points, using recent versions of OPM Flow, show that the AMD desktop is indeed faster than the Intel i5 laptop. The chart on the right helps to illustrate the point as the computation time per linearisation is higher for thhe Intel system, but it about the same for both AMD runs (despite the different OPM Flow version used). That's a relief.

The first two comparison points also show the number of overall linearisations that are computed during the run. It can be seen that between OPM Flow version 2022.04-rc1 and 2024.10, this has dropped from 14,015 to 3,614, but remains pretty constant to version 2025.04 with 3,600. Given that it is each linearisation that is taking the computational effort to solve the problem, this is a massive saving in time.

<figure>
	<a href="{{ site.url }}/images/Analysis/opm-flow-performance.png" data-lightbox="image-1" data-title="Performance improvements over time for OPM Flow reservoir simulator.">
		<img src="{{ site.url }}/images/Analysis/opm-flow-performance.png" alt="Performance improvements over time for OPM Flow reservoir simulator."/>
	</a>
	<figcaption><strong>Figure 1: Performance improvements over time for OPM Flow reservoir simulator.</strong></figcaption>
</figure>

It would be interesting to know what has changed in OPM Flow to deliver the improvement in speed. At thhe heart of a reservoir simulator is a large sparse matrix inversion. A number of different mathematical techniques have been devised over the years to quickly approximate the correct answer -- in lieu of an exact answer being very time-consuming to compute. Perhaps it is an improvement to the [Dune numerics](https://dune-project.org/) library used for the mathematical calculations that has yielded the speed up?

Of course it would also be interesting to benchmark this deck against _ECLIPSE_ and _tNavigator_, but since I don't currently have access to either of those products that will need to wait for another day.