---
layout: article
title: PVT Fluid Properties Tool
modified:
categories: pyrus
excerpt: Features of the PVT fluid properties editing tool.
tags: [pyrus_suite, netbeans, dynamic_modelling, simulation, software, programming, simulation_deck, ide, pvt, eos, fluid_properties, oil, gas]
image:
  feature: feature-pvt-fluid-properties-tool-1024x256.jpg
  teaser: teaser-pvt-fluid-properties-tool-400x250.jpg
  thumb:
comments: true
---

Over several decades the oil and gas industry has developed a wide range of correlations that are still regularly used to predict fluid properties based on high level measured characteristics such as API gravity and gas-oil ratios. In many cases, the use of these correlations is a case of near-enough is good enough, but in other cases the predictions turn out to be unreliable. What is really needed is a way to determine fluid properties based on a detailed description of the fluid composition. This is the purpose of an "equation of state" (EOS) and there are many approaches that have been proposed and implemented.

Formulations for predicting fluid properties using an EOS go back over a century and given that this is a field with utility to many industries, not just the oil and gas industry, it is unsurprising to learn that this has been the focus of intense research efforts. Despite this, a definitive EOS that accurately describes the fluid properties of any arbitrary fluid mixture, does not exist. In oil and gas it is common to use one of the variants of what is referred to as a "cubic equation of state". [An explanation as to how cubic equations of state are implemented in Pyrus is described elsewhere]({{ site.url }}//analysis/fluid-properties-and-equation-of-state/). This article focused on the features of the PVT tool itself and how to use it.

## Launching the PVT Tool

A standalone version of the PVT tool can be launched using the Tools / PVT / PVT Phase Envelope menu found at the top of the application window. This will open up a separate dialog window that allows for quick testing of properties associated with different fluids. Note that this standalone tool does not allow the saving of any results. It is purely a utility tool.

## Structure of the PVT Tool

The PVT tool can appear a little daunting at first as much of the functionality can be directly accessed from the dialog window that opens up. This is a deliberate design choice. Hiding much of the functionality behind other tabs or options provides a cleaner and less busy interface, but the trade-off is that it requires more clicks and / or keypresses to use the tool. Thus the interface actually gets in the way and the tool loses any benefits of being quick and functional.

The tool is structured into several areas as shown in the screenshot below:

<figure>
	<a href="{{ site.url }}/images/Pyrus/pvt-fluid-properties-tool/pvt-fluid-properties-tool-figure1.png" data-lightbox="image-1" data-title="Principle purpose of each area in the PVT fluid properties tool.">
		<img src="{{ site.url }}/images/Pyrus/pvt-fluid-properties-tool/pvt-fluid-properties-tool-figure1.png" alt="Principle purpose of each area in the PVT fluid properties tool."/>
	</a>
	<figcaption><strong>Figure 1: Principle purpose of each area in the PVT fluid properties tool.</strong></figcaption>
</figure>


