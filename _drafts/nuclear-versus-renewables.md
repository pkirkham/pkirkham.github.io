---
layout: article
title: Understanding Costs of Nuclear vs Renewables
modified:
categories: blog
excerpt: Calculating indicative power generation costs for competiting proposals.
tags: [energy, electricity, nuclear, renewables, solar, wind]
image:
  feature: feature-nuclear-vs-renewables-1024x256.jpg
  teaser: teaser-nuclear-vs-renewables-400x250.jpg
  thumb:
comments: false
---

In Australia there has been a lot of talk in recent months about nuclear versus renewables as a source for energy in Australia. When I say talk I really mean posturing and chest-beating. Let's be honest, most people aren't engineers and as soon as you start to point out the difference between the MW and a MWh, their eyes glaze over.

The thing is, to comment intelligently and knowledgeably on this topic, you don't need to have an advanced degree. You just need to have a basic understanding of high-school physics, maths, and an open mind.

So rather than pick sides, I'm going to set out in what is hopefully an easy to follow format, a basis for comparing the two options. Rather than telling you what you should be thinking, I'll show you how to calculate the pros and cons. You, my reader, can then decide for yourself based on your own calculations and interpretations.

Fair warning: this is a long post. TLDR: whilst it defeats the point of helping the reader think for themselves you can skip to the conclusion where I share my personal views based on my calculations.

## Background Perspective on Nuclear and Solar

If you ask children in the Western world today what big picture topic weighs most heavily on their mind, I'm fairly certain that after Fortnite and Minecraft you'd find "climate emergency" or some variation thereor mentioned.

How lucky we are to be parents today. When I was a kid we were terrified of nuclear winter. It's not that we weren't aware of the possible consequences of what was called "global warming" in those days, but rather there were more immediate and pressing concerns. The cold war was still going on, and the dystopian teen novels I read were all based around themes of emerging from nuclear shelters into the wastelands after the bombs had dropped. Just in case you weren't paying attention in class as a student back then, the world threw up timely reminders that you couldn't ignore: I vividly remember Chernobyl in 1986. I was fortunate enough for my parents to send me to a preparatory school in the South of England (boarding school), and we were told that the we were in the path of the fallout. No-one knew what was going on or what would happen. Radioactive rain was a fear that gripped the entire school community... and what would happen if the cows ate the grass that had radioactive rain fall on it. We'd have radioactive milk! The school thought it had better take precautions. We were locked inside and forbidden to go outside, and had to resort to powdered milk.

At least for three days until they decided that maybe the sky wasn't going to fall on our heads.

Although reactor 4 was destroyed, the need for large amounts of power meant that reactors 1, 2 and 3 were kept running until 2000. All said and done, had the Soviet Union not come to an end in 1991, it might well have continued for even longer. Clearly it was a desperate situation, but at the same time it was not an impossible one. Today there are people living at Hiroshima and Nagasaki in Japan, and wildlife has also returned to the exclusion zone around Chernobyl. It seems that nuclear disaster isn't necessarily game over.

### At First Glance

A confession. This isn't the first time I've looked at solar and nuclear. Most of my life has been involved working with sources of energy. Today I work in the oil and gas industry, but my first interest with energy started, like many kids, with high school science projects.

Sadly these projects didn't involve building my own reactors. I remember they were written (with a pen and paper -- do kids still use them?) projects where you had to research a topic and supplement your work with copious amounts of equations to demonstrate that you understood the physics involved. This was in the days before the internet, so almost all research involved textbooks, library visits and trying to work out what equations to apply to different situations to understand what was going on. These days I'm sure you can just find out what you need from the internet. I can't help feeling that without having to do things the 'hard' way, kids aren't learning as much today.

Whilst I don't have my original projects to copy from, much of this blog post will undoubtedly retread familiar ground. After all, it's not like the physics has changed as I've grown older.

### Bringing Dreams to Reality

I'm fortunate that I was recently able to build my "forever home". When I was at school, one of the science projects I had undertaken was into solar energy. Strangely, for reasons I can't remember, I didn't research much into generating electricity from solar. It's most likely that in the late 1980s, generation of electricity from solar still had a long way to go. What I did focus on was passive house design, and harnessing conduction, convection and radiation to regulate the temperature in a home. This taught me many fundamental principles, and I was able to [implement several of them]({{ site.url }}/blog/becoming-an-accidental-architect/) on [our own home design]({{ site.url }}/blog/from-vision-to-reality/).

The point is that I'm no novice when it comes to solar. I've always been fascinated by it, and I've walked the walk, not just talked the talk. My house currently has 19.8 kW panel capacity, a 13.5 kWh battery and inverters for the three phases at our property. Whilst not completely off-grid, it is semi-off grid, and the system design allows the battery to recharge and run back-up circuits even if the grid is down. On Christmas Day 2023, we were able to test this for real when a storm hit our area, and the grid was down for 10 days. Much of what I will present on solar is not just theory, but is based on real-world experience (and a passion for the technology).

### A Nuclear Renaissance

So what about nuclear? I work in the oil and gas industry, not the power generation industry. What on earth would I know about nuclear power generation?

Well, for starters I did read Engineering at the University of Cambridge, which sets you up well for pretty much any technical discussion. Most power plants, particularly those that revolve around creating steam and driving turbines, are fundamentally based on the laws of thermodynamics. This is bread and butter stuff for an engineering student.

But what about nuclear physics itself? Again I have to go back to the days of doing projects at school. Against a cold war backdrop, I paid particular attention to our lessons on nuclear physics. I was convinced this was where the future of humanity lay. Once you understand how much power you can harness from the atom, everything else seems trivial. So of course one of my high school science projects delved deep into nuclear reactor design, supported by many calculations to demonstrate that I understood how the thermal energy was being converted into electrical energy. I think if my parents weren't both in the oil and gas industry, there is a good chance I'd have ventured into the nuclear industry.

Fast forward to the present day. I love cycling. Whilst you're pedalling on, it's normal to have a good chat with your riding partners, across a wide variety of topics. Near the start of 2024, before Peter Dutton had made his announcement concerning a policy to build nuclear plants in Australia, one of my cycling conversations turned to the very topic of nuclear power. Aha, now here was something I could really talk about with some passion. Having expounded the virtues of nuclear power and provided a few rambling technical arguments as to why it made a lot of sense from an engineering point of view, I was asked what I thought it would take for nuclear power to be adopted in Australia.

Dang. Great question.

I'd never actually considered this before. I mean, why do we not use nuclear energy in Australia? Just about every other developed economy in the world does. I figured it was because historically there had been opposition to installing nuclear reactors in Australia, and that essentially this wasn't a vote winning proposition. The logical conclusion was that once politicians saw that it could be a vote winner, then the tide could turn and we'd see nuclear being promoted as a viable option. The surprising part was that we didn't have to wait long for [Peter Dutton to propose building nuclear plants in Australia](https://www.abc.net.au/news/2024-06-20/nuclear-dutton-coalition-unanswered-questions-beak-rules/104000664).

## Basic Concepts in Energy

- Define key terms: energy, power, and efficiency.
- Explain the difference between different types of energy, such as thermal, electrical etc.
- Discuss the concept of energy density and its importance. Chemical energy, such as oil or uranium is very dense. Renewables tend to be more distributed.

### Energy Trilemma

The energy trilemma helps to frame how we think about different sources of energy. It's all very well to calculate the various costs involved in producing energy but in isolation these can give a misleading picture when comparing one technology to another. What is needed is a holistic framework through which we can appreciate the different trade-offs.

Quite often the term energy trilemma is used. I note that people will usually nod sagely when this is discussed, but I have a sneaking suspicion that they don't really understand what a trilemma is. I accept that sounds quite arrogant, and it is. I won't apologise for that though, because if you ask most people about a trilemma they'll mention the three things that are important, and go on to say that you need to achieve all of them.

I'll have to apologies for raining on the parade of everyone that believes that, because that's not how a trilemma works. It's about trade-offs.

In contrast, most people are familiar with dilemmas. There are two competing options both of which are desirable. However, you only get to choose one or the other. Where both choices offer similar positive benefits, often the choice can come down to "the lesser of two evils", where the least bad choice is made. So how does this relate to a trilemma? With a trilemma, instead of just two choices, now there are three possible outcomes that you want to maximise... but you can only pick two. You can't have all three.

Often a fun way to think of a trilemma is with cars where the three choices relate to cost, quality and speed. A cheap and reliable car won't be fast (Toyota Corolla etc.). A cheap and fast car will probably break down a lot (Lotus, TVR etc.). A fast luxury car is most certainly not cheap (Ferrari, Bugatti etc.).

Let's look at this through the lens of our energy trilemma. What are our three outcomes that we want to maximise?

  - **Cheap:** There are two aspects of cost to consider. The first is the up front capital cost to build and install the energy generation technology. This can vary enormously, from a very cheap solar panel generating a few Watts, through to GigaWatt-scale hydro and nuclear plants costing billions (in any currency). Here the cost of labour and materials are important but separate considerations. The second consideration is the operating cost. Once built, how much maintenance and operations cost must be expended to keep it running and generating electricity?
  - **Reliable:** Reliability covers several different aspects. To express it simply: when we want energy, it must be available. Not too many people would disagree with that. But there are many aspects to this, all of which are important. Firstly, energy should be instantaneously available -- that is to say it can respond rapidly to changes in demand. Secondly, it should be stable. The overall grid demand may be constantly changing, but at any instant all the users of power expect it to be steady and unwavering (no brownouts). Thirdly, it should be dependable. The grid has to run all hours of the day, every day of the year, to meet whatever demand is required. If it cannot achieve this, there will be blackouts; rolling blackouts are used in some places to share the pain of a grid not being able to supply 100% of demand, and in an extreme case a total blackout occurs where complete grid failure leads to no power being delivered.
  - **Sustainable:** In today's hyper-climate concerns dominating environmental discourse, it is not surprising if the first thought that comes to mind when considering the sustainability of an energy source is the degree of carbon dioxide emissions that are produced. These are far from the only sustainability issues that we should consider. In addition to carbon dioxide emissions, there are other (real) pollution concerns that arise from energy generation, such as land impacts, waste, material requirements and disposal. The practical longevity of the energy source must also be considered. Some fuel sources are finite, and will not last indefinitely.

Cheap, reliable and sustainable energy. The holy grail. Work out how to deliver all three and you're probably a god. For us mere mortals, we are left with a classic trilemma.

For most people in the world, whether they openly admit it or not, what they really want is cheap and reliable energy. This is the foundation upon which all of the world's most developed economies are built. This is not a secret or some great insight. It's well known and recognised. Cheap and reliable energy lifts societies out of poverty. It is a force for good. The fossil fuel sources of coal, oil and natural gas have played a massive role here in the development of all of the worlds developed economies. It is only natural that all countries would like to harness this for their populations. Almost all human migration patterns, both legal and illegal, are towards countries where economies were built on cheap and reliable power. This is no co-incidence.

In many developed economies, attention has turned towards an increased demand for sustainability. Democratic governments, listening to the cries of their voters, have hustled towards adoption of cheap and sustainable energy. Wind turbines and solar panels have proliferated. In doing so reliability is being tested. It may not seem so whilst the renewables are supported by a back-up grid that can step in to meet demand at night-time or when the wind isn't conveniently blowing, but without that grid, the reliability of intermittent renewable sources is non-existent.

That isn't to say that intermitted renewables cannot be made reliable. The simple approach is to build more capacity than is required on average, and to store the excess energy for days when generation cannot meet supply. In effect we average out the highs and lows of generation in order to deliver a steady power into the grid: a reliable and sustainable solution. Yet it is far from cheap, as to achieve this outcome it is necessary to build more excess capacity plus storage for the longest period that could statistically be required. In effect you are over-investing just to cover the rarest worst case scenario. Alternatively you can spend huge sums of money on large clean power solutions: hydroelectric dams and nuclear power plants. They are undoubtedly reliable and sustainable, but you'd be hard pressed to argue that the initial capital investment makes them cheap.

So, now that the real meaning of the energy trilemma is understood, and it is accepted that you can't have all three desired outcomes, which two would you pick? Be honest.

## Overview of Nuclear Energy
- Describe the basic physics of nuclear fission.
- Explain how a nuclear power plant works.
  - Reactor
  - Steam Generator
  - Turbine
  - Cooling System
- Discuss the energy density of nuclear fuel compared to other sources. How much uranium is needed to generate how much power?

## Overview of Renewable Energy
- **Solar Energy**
  - Describe the basic physics of solar photovoltaic (PV) cells.
  - Calculate how much solar energy flux there is on the planet EVERY DAY. It's huge!
  - Explain how solar panels convert sunlight into electricity.
  - Solar irradiance principles and calculation tools
  - Actual output versus nominal nameplate capacity
- **Wind Energy**
  - Wind energy is really just indirect solar energy. Winds are generated by difference in pressures, which are related to temperatures.
  - Describe the basic physics of wind turbines.
  - Explain how wind energy is converted into electricity.
- **Hydropower**
  - Again, hydropower
  - Describe the basic physics of hydroelectric power.
  - Explain how water flow generates electricity.

## Comparative Analysis
- **Energy Density**
  - Compare the energy density of nuclear fuel, solar energy, wind energy, and hydropower.
  - Land use requirements for different sources.
- **Efficiency**
  - Discuss the efficiency of different energy conversion processes. Total energy consumed in manufacture and installation versus energy returned over lifetime?

### Reliability

'Dunkelflaute' is a German word meaning "dark doldrums": a period of several still and cloudy days where wind turbines and solar panels steadfastly refuse to generate much power at all. It has entered into the parlance of the renewables world as Germany was a leading proponent in adopting renewable energy sources, including wind and solar. The German Chancellor, Angela Merkel, took the decision in 2011 to shut down the country's nuclear power reactors, thus exposing the country to the consequences of dunkelflaute. Unfortunately, the [reality of transitioning to a grid supplied mostly by intermittent renewables](https://jacobin.com/2024/10/germany-green-energy-transition-labor) is starting to make itself known.

Since the absence of sunlight at night and periods of no wind are not unexpected, it is reasonable to conclude that all we need to do is to plan for these periods. Provided that we have stored sufficient excess energy ahead of these periods, we can use this to bridge the periods of low energy generation. This might sound simple on paper, but [in practice the energy storage requirements are anything but simple](https://physicsworld.com/a/why-we-need-to-tackle-renewable-energys-storage-problem/).

- - **Land and Resource Use**
  - Compare the land and resource requirements for nuclear and renewable energy sources.
  - Additional land and infrastructure access costs for distributed wind and solar (for grid-scale projects). Rooftop solar does not have these costs!
- **Environmental Impact**
  - Discuss the environmental impact of nuclear waste versus renewable energy sources. Visual pollution comparison. Volume of waste generated and disposal considerations. Accidents. 

## Economic Considerations

### Cost of Electricity Production
  - **Levelized Cost of Energy (LCOE):** This is the average cost of generating one unit of electricity (e.g., one kilowatt-hour) over the lifetime of the plant, considering all capital, operational, and maintenance costs. LCOE is calculated as the total cost of the project divided by the total energy produced over its lifetime. This metric is widely used to compare different energy sources on a consistent basis.
  
  - **Levelized Full System Cost of Energy (LFSCOE):** While LCOE provides a useful benchmark, it falls short for renewable energy sources like solar and wind, which cannot provide on-demand power 24/7. To ensure a steady 24/7 capacity, additional investments in energy storage systems, such as batteries or pumped hydro, are necessary. LFSCOE includes these additional costs, offering a more comprehensive view of the true cost to deliver consistent and reliable power. The total cost to deliver a steady 24/7 capacity is much higher than the LCOE, reflecting the need for energy storage and grid integration.

- **Capital and Operational Costs**
  - Compare the initial investment and ongoing operational costs for nuclear and renewable energy sources.

- **Subsidies and Incentives**
  - Discuss the role of government policies and incentives in shaping the energy landscape.

## Comparative Electricity Costs
- **France**
  - France has a high nuclear penetration in its energy mix, which generally results in lower electricity costs. As of November 2024, the average price per kWh in France is around **0.28 AUD**. This is relatively low compared to many other countries.

- **Canada**
  - Canada also relies heavily on nuclear power, along with hydroelectricity, which keeps its electricity costs relatively low. The average electricity cost in Canada is about **0.23 AUD per kWh**, making it one of the more affordable options globally.

- **South Australia**
  - South Australia has a high penetration of intermittent renewables like wind and solar power. This can lead to higher electricity costs due to the need for backup power sources and grid stability measures. The average electricity cost in South Australia is around **0.26 AUD per kWh**, which is higher than both France and Canada.

## Future Trends and Innovations
- Briefly discuss ongoing research and innovations in both nuclear and renewable energy technologies.

## Conclusion
- Summarize the key points.
- Emphasize the importance of understanding the underlying physics and economics before forming an opinion.
- Encourage readers to explore further and make informed decisions.

### My Personal View

Aside from the introductory background to the topic, I've been careful throughout this post not to let my opinions colour the objective realities.

## References

 - Idel, R. 2022. Levelized Full System Costs of Electricity. _Energy_. **259** (2022): 124905. [https://doi.org/10.1016/j.energy.2022.124905](https://doi.org/10.1016/j.energy.2022.124905).
