---
layout: article
title: Integrating Project Definition Into Cost Estimates
modified: 
categories: pyrus
excerpt: How Pyrus translates uncertainty associated with project definition into cost and schedule estimates.
tags: [projects, fel, definition, cost, schedule, planning, cost_estimate, stochastic_simulation, monte_carlo, pyrus]
image:
  feature: 
  teaser:
  thumb:
comments: true
---

There are two problems apparent with current cost estimation techniques. Firstly, whilst we would like to see a narrowing of the confidence around the cost estimate with the most likely value remaining constant, what we tend to observe is instead a gradual rise in the cost estimate as project definition improves. The second problem is that Monte Carlo simulation, as used to help set contingency on projects, does not seem to really add any value other than providing a warm and fuzzy feeling that the magic 10 percent number is correct. Yet stochastic simulation methods as applied to probablistic schedule analysis do seem to drive more realistic project schedules.

## How Project Definition Manifests Itself Within Projects

[cost estimate growth during FEL vs cost growth during Execution, scope changes -> cost growth, late changes -> schedule delay -> cost growth]
[scope changes during FEL will lead to a revision in project cost and schedule, but scope changes during Execution will lead to numerous late changes in design that blossom into schedule delays and cost growth. therefore scope changes should only be permitted to occur early on in the project definition phase i.e. before concept select gate in FEL 2.]

## Modelling Uncertainty Related To Project Definition

[poorer definition reflects less understanding regarding the various project elements, and thus there is a higher degree of uncertainty concerning outcomes related to the poor definition. the degree of this uncertainty may not be readily recognised in the deterministic base case cost and schedule estimates.]
[Creation of scope and schedule-driven cost estimates rather than purely factored cost estimates. The unit rates can be factored, but ultimately the cost estimate is calculated from the scheduled activities. This will need to take into consideration both LS and reimbursable strategies. Changes to a project will therefore either add scope (additional equipment, cost, rework) and thus changes to LS and reimbursable, or sequencing of activities may get out of step, leading to waiting on previous activities or carry-over of work offshore etc.]
