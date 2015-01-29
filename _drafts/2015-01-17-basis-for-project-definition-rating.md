---
layout: article
title: Creating A Project Definition Rating
modified: 
categories: pyrus
excerpt: Explaining the basis and nature of the categories that are behind the project definition rating used in the Pyrus Suite.
tags: [projects, fel, pdri, definition, data, context, engineering, planning, pyrus, ipa, cii, aacei]
image:
  feature: 
  teaser:
  thumb:
comments: true
---

A project definition rating is used by the Pyrus Suite to model the phenomenon whereby a project team fails to fully recognise all the uncertainty associated with a project, and thus the cost, schedule and production estimates that are produced tend to be too optimistic. This optimism bias is a well recognised human trait, and rather than try to change or eradicate the bias, the approach used is to understand the likely extent of the bias, and adjust our estimates accordingly.

The Pyrus Suite is an integrated project simulation tool that unifies the various tripartite cost, schedule and quality outcomes into a single environment. This allows the subtle (and obvious) relationships between these outcomes to be captured. For example, a delay in drilling rate of penetration will be captured as an increase in the drilling duration. But the drilling duration is not the only outcome that is affected. The delay will also affect the following:

* Increased drilling duration leads to increase in drilling cost for wells drilled on a reimbursable day rate basis
* For wells that are drilled after first hydrocarbons, a longer drilling duration will lead to a longer ramp-up period for wells being brought on-stream and thus production will not reach plateau as rapidly
* For wells drilled prior to first hydrocarbons the longer drilling duration could extend drilling operations so that they clash with construction, installation or commissioning activities, leading to a knock-on cascade of changes to the schedule and thus associated cost of the project.

In other words the seemingly innocuous delay from a longer drilling duration can affect all outcomes of interest: cost and schedule for both facilities and wells, and the production profile that is achieved. Without a tool that properly models the interaction between the different elements, it is hard to capture the consequence of a change to the project as it ripples through the system.

## Existing Project Definition Measurement Approaches

There are methods that have already been developed to measure project definition, and which have found varying degrees of adoption across industry. These approaches include the Front End Loading (FEL) Index developed by [Independent Project Analysis (IPA)](http://www.ipaglobal.com) and the Project Definition Rating Index (PDRI) developed by the Construction Industry Institute. Both these methods have been validated using statistical methods against actual project data.

### Independent Project Analysis FEL Index

The FEL index as used by IPA comprises between three to five categories, each containing a number of elements that are assessed for a project.

IPA is a private company and the underlying basis for the FEL index is not published in detail. The elements that are examined and then scored in order to calculate the FEL index have been described in various sources. Thus it is possible to determine what aspects of a project are important considerations with respect to definition (since this information has been published). The calculation methodology that describes how relatively better or worse definition for individual elements is translated into a FEL index is confidential.

The FEL index ranges from 3 to 12, a scale that seems arbitrary, but for which a descriptive word rating scale is often superimposed, with the boundaries between different categories chosen by an analysis of historical project performance from sanction to first hydrocarbons. IPA's research shows that FEL correlates strongly with all project outcomes e.g. projects that are sanctioned with a lower (better) FEL index correlate against a smaller growth in project cost between sanction and first hydrocarbons. What IPA refers to as the _Best Practical_ range for projects, is the optimum range that projects should seek to achieve prior to sanction. This range is typically between 3.75 to 4.75.

These relationships are well accepted in industry, but what is less often publicised is that the scatter of individual project results around each relationship trend line is quite broad. So whilst the trend is statistically a very strong relationship, it is not possible to use the FEL index in a quantifiable predictive manner (at least without access to the underlying data which are confidential).

### Construction Industry Institute PDRI Index

The methodology to calculate the PDRI is available from the CII and the development of its indices are also described in several academic papers. The PDRI for industrial projects comprises 70 elements that were identified and categorised through literature review and assessment of industry practices. The relative weightings of different components were determined through solicitation of industry expert opinion. The result is a score than ranges from 70 (completely defined) through to 1,000 (no definition).

Whether the expert opinion, used to weight the elements for calculation of the PRDI, is correct is a matter of debate. As a cynical ex-IPA analyst I might scoff at the idea of any weighting greater than zero for value improving practices, but generally the weightings appear sensible. For example the highest weightings are given to project requirements, site information and project strategy. To those unaccustomed to the world of industrial projects it might seem odd that the actual design of the facility, into which many engineering hours and cost will be sunk, is not at the top of the list. But it is rarely bad engineering that trips a project up. On the other hand, designing the wrong facility because the project objectives were not clear, or running into trouble when the first piles are driven because of a geotechnical surprise, are more frequent occurrences than most would like to admit.

Nonetheless various academic tests of the index have been carried out, which suggest that a lower index is associated with better project performance. Specifically there were statistically different outcomes for projects with a PDRI score below 200 in comparison to those over 200. Interestingly if you perform a linear mapping of the PDRI scale onto the FEL scale, a PDRI of 200 is approximately 4.25 which is bang in the middle of IPA's _Best Practical_ range. The equivalent _Best Practical_ range for PDRI would be 150 to 250. This suggests that despite their differences, both approaches share some consistency.

## Rationale For Developing A New Project Definition Metric

There are already at least two existing approaches to quantify the degree of project definition. Why does the Pyrus Suite need to consider yet another metric; a metric that has not been verified or created using a historical database of actual projects?

Whilst it might be possible to create and approach that closely mimics the IPA FEL index, given the very broad relationship between project outcomes and the index, and the confidential nature of the project database used to generate the index, there is little purpose in doing so. The CII PDRI Index could be implemented, but the index is tailored predominantly towards the construction industry, and thus the wells and reservoir disciplines would be neglected.

So there appears to be no compelling reason to replicate either approach.

It should be noted that both the FEL index and PDRI are intended to be used as a broad indication of the project definition. In both cases a lower score is indicative that the project is likely to have a better outcome. Note the use of the term "is likely" instead of "will". Whilst the indices are useful measurements of a project's definition, they do not facilitate any in-depth modelling of project outcomes. They are crude indications only, and it would be a leap of faith to translate a single index into a project simulation environment. In other words, with a single index number it is not possible to distinguish between:

1) Cost growth because of poor planning that cascades into execution problems, or 
2) An engineering design assumption was later proved incorrect leading to the addition of missing scope into the project.

However with a project simulation environment it is necessary to determine the probability and the potential influence for both of these scenarios (amongst hundreds of potential scenarios) that arise from a project's definition.

Interestingly both the FEL and PDRI methods take a similar approach to measuring the various elements of project definition. For each element there is a definition of what is being measured, and a numerical rating scale is applied to the element using detailed descriptions for classes of definition that might apply to the element. In both cases this should be an objective approach for most of the time, although grey areas will always appear leading to a small degree of subjectivity. Overall the approaches taken are repeatable, which is an important aspect for any approach that intends to be more widely adopted than the original creator.

The Pyrus Suite utilises a similar approach; breaking down a project into different elements and categories, and assigning a numerical score to each element based on pre-defined descriptions. Since the drivers of project performance are known, a custom project definition has been created that draws upon established relationships. This is a straightforward first principles approach that does not introduce any implausible or unproven relationships.

The definition rating elements can then be used to to probabilistically perturb various project inputs as part of a Monte Carlo simulation. The distribution ranges around different elements that are arbitrary, meaning that there could both bias (either optimism or pessimism) and overconfidence implicit in the degree to which any project outcome is modelled relative to an input. However, provided the distribution ranges are transparent, and can be adjusted, the approach would permit fine tuning and calibration against actual project results at a later date. Besides which the hypothesis and rationale behind Pyrus is that it is the emergent properties from the interaction of project elements that matters more than the individual input distribution ranges for those elements.

The first step is to create the tool. Perfecting the tool can come later.

## Identification Of Project Definition Categories

An examination of the literature that describes the elements of FEL and PDRI reveals that both indices have elements in common.

The IPA FEL index can be broken down into several major categories, which vary slightly depending on the type of scope that is being evaluated. The first category covers the nature of the project site (including the nature of the site geotechnical character, plot plans and regulatory constraints e.g. permits), the influence of any external stakeholders e.g. local content requirements, and an understanding of the available contractors and their capability to execute the scope. The second category concerns the level of detail that has been achieved with the engineering definition. For oil and gas facilities this can be measured through examination of the technical drawings such as process flow diagrams (PFD), piping and instrumentation diagrams (P&ID) etc. For the subsurface or reservoir engineering discipline it might instead refer to the geological static model and dynamic numerical simulation models that have been built. The final component is the project execution planning (PEP). A large part of PEP is the project schedule, and research shows that a networked and resource-loaded schedule, that has been built and is managed by the project team (not a contractor), and incorporates all disciplines (not just the facilities) is correlated with the best project outcomes.

The CII PDRI can also have its various elements grouped into a few high level categories. The first category is the basis of the project decision, which includes an assessment of the project strategy, owner/operator philosophies, project funding and timing, project requirements, and value analysis. Value analysis appears analogous to activities that are sometimes referred to as value improving practices or VIPs. Specifically in the PDRI it includes value engineering, design simplification, material alternatives and constructability reviews. The second category is the basis of design, which includes site information (including geotechnical characteristics, environmental and permitting requirements, surveys etc.), location and geometry, associated structures and equipment, and project design parameters. The final category is the execution approach, which includes the land acquisition strategy, procurement strategy, project control, and project execution plan. Curiously the PEP does not contain an element that measures the definition associated with the project schedule. Maybe schedule is irrelevant in the construction industry? Actually it is captured under the project funding and timing category.

There is a large degree of overlap between the two approaches. It appears that almost all the IPA FEL elements are present in the CII PDRI, with the exception of local content requirements. Conversely there are several elements in the CII PDRI that do not appear in the FEL index. Some of these, including project objectives and VIPs, are included in separate metrics that IPA produces. Others, such as owner and operator philosophies, are not usually considered in the FEL index.

In creating the project definition elements for Pyrus, the FEL and PDRI categories have been used to guide the conceptual nature of categories used.

### The Four Primary Classifications For Project Definition Elements

All definition categories are essentially answering the "why?", "where?", "what?" and "how?" of the project. If you can answer those questions then the project should be considered well defined. Using these questions to guide the elements for each category has been chosen as the basis for the Pyrus project definition rating, principally because it is simple and easy to understand. There is also a logical progression to the questions. First we must consider the 'why' and 'where' of the project, and with this knowledge we can proceed to determine 'what' solution is required, before finally planning 'how' we will implement the solution.

Each category has been broken down into six sub-categories. There is nothing magical about the number six, other than when considering what the important areas are for each main category, this is the number than naturally emerged. There is an element of subjectivity of course. For example the 'Business Objectives', 'Project Objectives' and 'Discipline Objectives' could be amalgamated into a single 'Objectives' category. It is my view that these sub-categories are very different, and the crucial part is that the definition for each could be different and will thus each will have a different influence on the project outcomes.

The intention is that the categories and sub-categories are suitably broad so that they can be applied to any one of the three main oil and gas disciplines with no modification e.g. reservoir, wells and facilities. Obviously some of the sub-categories will need to be interpreted in accordance with the discipline e.g. for 'Engineering Definition' under 'Technical Evaluation' we are considering the geological modelling and reservoir simulation work, whereas for facilities we are considering the process engineering and structural design.

#### Measurement Of Basis Of Project (WHY)

Every project should have a purpose; if there is no purpose then arguably it is not a project, but a... um... sophisticated exercise involving the flushing of large sums of money down the toilet? Problems arise when the reasons why an aspect of the project has been chosen are subject to uncertainty. These are the circumstances under which assumptions are necessary (for without them progress will be all but impossible) and the project becomes exposed to the potential for change as a result.

Changes that emerge from incorrect assumptions related to the basis of the project are rarely small. Often they will lead to changes in scope, addition of scope or even wholesale change to the entire concept -- that is if the entire project is not recycled or cancelled. Thus the consequences of poor definition in this category will vary depending on the stage of the project. If the project is early in FEL 2, then lack of clarity around objectives and missing basic data can be addressed. On the other hand, if these are not addressed prior to sanction and it is discovered that the assumptions were incorrect during execution, then we have a situation for complexity emergence a.k.a. "the shit hits the fan".

It is also worth noting that the direct consequences of poor definition in this category, if addressed early in the project process, will mostly affect cost and schedule. This is because additional scope is usually required. If the poor definition is not addressed until the Execution phase, then not only will it affect cost and schedule, but it will also likely affect quality and thus production. This is because the project will be forced to accept compromises in design that are simply impossible to implement once fabrication has started. Remember that it is always easier and cheaper to make a change on paper than it is with steel.

Basis of Project Rating (BPR) Sub-categories:

* **Business Objectives:** The business objectives are at the heart of any project. These define the whole rationale for the project. Where the business objectives are unclear there is a large risk that the wrong project will be done, and when this is belatedly realised there will be a demand to change the project to suit.
* **Project Objectives:** The project objectives flow on from the business objectives. These translate the business objectives into scope such as functionality and capacities. A key part of the objectives is to provide guidance as to how to balance cost, schedule and quality. All projects have to trade-off these three outcomes against each other, and if this is not done effectively then expectations may be set too high a.k.a. "want to have your cake and eat it".
* **Discipline Objectives:** The discipline objectives are rated separately for each discipline: reservoir, wells and facilities. These refer to a more detailed aspect of the project objectives, as they apply to each discipline, and consider the trade-off decisions that must be contemplated. For example with the wells discipline should the design try to optimise the drilling rate of penetration at the expense of hole quality, or drill more slowly to preserve the hole and improve logging, running casing etc.? How many wells are required and what are the surface location and bottom hole targets? Another common decision is the opex/capex trade-off, and example of which is the use of a more expensive corrosion resistant alloy (capex) or the use of chemical inhibitors (opex).
* **Basic Data:** At first it might seem that basic data is in the wrong category; would this not be more logically located in the project context rating? However an understanding of the basic data is a fundamental driver of why we choose our concept and scope. If we recognise there is corrosive gas in the reservoir, then we know we need to design for that. A good knowledge of the metocean conditions for offshore field developments will guide what concepts are possible. Again different basic data needs are assessed for each discipline. Getting the basic data wrong can have serious implications for any project, and can often lead to significantly reduced production until additional process scope (usually forming a second project) can be added to the facilities.
* **Commerciality:** It goes without saying that the whole purpose of most oil and gas developments is to make money. If this were an easy game, everyone would be doing it. Sadly it isn't: but therein lies the opportunity for those that can play the game well! When projects are marginal, meaning a small increase in capex, slip in schedule, lower production and/or reserves or a fall in the oil/gas price results in a project that is uneconomic, then this is an environment in which a project can be enticed to take shortcuts. This sub-category measures the ability of the project to absorb changes with respect to its overall economics.
* **Significance:** A former colleague of mine once explained that there are three types of projects. 'Ordinary' projects, 'Strategic' project and 'Prestige' projects. The latter two are more likely to throw reason out of the window, and for those that are classed as both strategic and prestige, one wonders if there is any hope at all? In all seriousness, the significance of any project within a company's portfolio, or to a host government, can attract a great deal of attention. If there are no plans in place to manage expectations, then this opens the door to late changes. The more significant a project is, the more effort must be expended to explain the proposed concept to all stakeholders.

#### Measurement Of Project Context (WHERE)

Consider two different projects with identical scope and fields to be developed. Whilst the technical characteristics are identical, where these projects are built and installed will greatly influence the ease with which the project can be executed. This is because there are both physical and human constraints that are imposed on a project, simply because of where it is.

Constraints limit how easily the project can be implemented, and they must be adhered to. They are inviolate in that it is nearly impossible for a project to change a constraint, and any attempt to do so can literally be equivalent to moving mountains. Recognition of the projects constraints is critical and a healthy respect (e.g. schedule float) and incorporation of any requirements into the execution plan should be made. A failure to do so is likely to lead to a delay in schedule, whilst the problem is rectified. Delays that arise from misunderstood requirements associated with this category can be very lengthy indeed.

Whilst the direct consequence of poor definition in the project context definition category is schedule delay, it must be recognised that this can have an indirect affect on both cost and quality. This is especially true if the delay in schedule cannot easily be accommodated because some hard deadlines were previously set -- these are fertile circumstances for extraordinary cost growth and serious production problems.

Project Context Rating (PCR) Sub-categories:

* **Site Location:** This sub-component does not refer to the site conditions e.g. nature of the soils etc. which is measured by the 'Basic Data' sub-component in the BPR. The site location will affect many different aspects of the project. A remote site will require extensive logistics e.g. an extreme example is Uzbekistan where your material import options are across the Caspian and then overland through Turkmenistan, or the two nearest ports in St. Petersburg or Vladivostok. What are the space constraints at the site? Can the plot plan be expanded if required, or is there limited space available -- this is a particular consideration for revamp projects. Has the land been acquired, or are there uncertainties in ownership that must first be addressed?
* **Contractor and Vendor Evaluation:** Owners can sometimes take a rather cavalier attitude towards contractors, and expect, nay demand, first class results to be delivered. Even if the owner is the root cause of problems on a project, it is usually the contractor that gets the blame. Rather than expecting contractors to hold magic wands, a better approach is to evaluate the available contractors and equipment vendors that a project might use. This will identify the yard availability and capability, lead times for equipment, and can guide the level of QA/QC that may be required. Owners and contractors need to work together, and if this is not done, then an owner should expect change orders, a slip in schedule and quality problems upon eventual delivery.
* **Local Content Requirements:** Many countries have local content requirements that a project must adhere to. When managed well, local content can be a benefit to a project. When it is only given lip-service then it is likely that there will be lower productivity as a result of bringing poorly trained people into the project simply to meet a quota.
* **Environmental Considerations:** Two things to note about environmental considerations. 1) If ignored they can completely stop a project. 2) Complying with the regulations takes a long time which has to be included in the project plan and schedule.
* **Regulatory Constraints:** Knowing all of the regulatory requirements is essential in order to avoid designing a solution that does not meet all the requirements. Examples of constraints in this sub-category include whether or not flaring is allowed, regulations regarding overboard discharge of water, health and safety requirements etc. Since the regulations will affect the design, it is important to gain a complete understanding of all regulations at the very start of a project. Different regulatory constraints may apply for different disciplines.
* **Permitting Status:** Permits are related to the 'Environmental Considerations' and 'Regulatory Constraints' but are given their own sub-category simply to reflect whether or not the necessary permits have been obtained. Going ahead with a project where the permits are not in hand, and where the issue of permits is not a trivially routine task, is akin to playing with fire. Different permit statuses may apply for different disciplines.

#### Measurement Of Technical Evaluation (WHAT)

Once we know why we are doing the project and what the end result needs to achieve, and we have considered where the project is located, then we can start to design the solution. Considering what the project will look like, in the absence of any knowledge of the why and where of the project, is equivalent to designing in a vacuum. Amazingly it happens quite a lot, and no matter how technically excellent the solutions are, there is always a risk that they are worthless as a result.

The cost, schedule and quality (production) estimates associated with a project are directly influenced by the technical work and interpretation of data. It is a natural human tendency to err towards optimism, and this optimism bias can lead a project to set targets are are aggressive (in the best case) and unrealistic (in the worst case). This is not to say that aggressive targets are necessarily bad, but when they are arrived at because certain aspects of the project have been overlooked, then they tend to be unachievable in practice. Unrealistic targets on the other hand are just fundamentally unachievable.

A consequence of increased technical definition, is that a greater understanding of the project is achieved. It becomes harder to deny that undesirable aspects of the project might exist, and that changes to the cost, schedule and quality targets are therefore necessary. In other words an improved understanding of the technical aspects of the project forces more realistic targets to be set.

What are the consequences for poor technical evaluation definition? There is no general answer in this category, as it will vary depending on the sub-category and the discipline to which the category is being applied. As a general statement it could be said that poor definition means the targets that are set are more likely to be unrealistic or too aggressive, but the knock-on effects that emerge from the project reverting to more realistic outcomes will depend on how the project has been planned (see the next category).

Technical Evaluation Rating (TER) Sub-categories:

* **Scope Completeness:**
* **Technological Innovation:**
* **Engineering Definition:**
* **Ancillary Tasks:**
* **Design and Operating Philosophies:**
* **Documentation:**

#### Measurement Of Execution Planning (HOW)

Effective planning can make or break a project. Unfortunately it is often done poorly, and leading to a large degree of grief for many projects. Of course we need to have some idea regarding exactly what it is we are planning for, and thus assuming we have good definition around the 'why', 'where' and 'what' of our project, we can finally work out 'how' we will implement it.

By their very nature most planning tools are deterministic in nature, and the baseline that is produced can lull us into a false sense of security whereby we are tricked into believing that this is how the project will proceed. For simple projects that is perhaps a reasonable belief, and indeed for well managed complicated projects it might also appear that the belief is justified. The reality of projects is that they are constantly bombarded by events that can dislodge the path on which a project is proceeding. Unless a project team has excellent foresight as to where the project is heading, and is supported by the right tools to steer the project back on course, it is quite easy to veer off into the unknown. When that happens the project becomes complex and unexpected problems behind to emerge.

So the measure of good execution planning definition for a project is one where the project team is able to respond to monitoring of the project and control it, as opposed to being mere passengers that are observing and reporting to management on the state of disarray that the project has plummeted to.

One theory, that appears to be validated by some successful projects which exhibited this trait, is that it is communication across the various project disciplines that enables successful outcomes. Through effective interaction, all project disciplines are aware of how they might benefit or harm other areas of the project, and this allows the project team to work towards a common objective. A consequence of poor execution planning definition is therefore one whereby the results that arise from the interaction of project activities are amplified.

For example if it is discovered that additional wells will be required (poor definition concerning 'Scope Completeness' for the reservoir discipline), then this will directly lead to a schedule and cost increase associated with those additional wells. If the project has a well developed schedule, and this possibility had been communicated to all project team members before sanction then this can be managed. Depending on how severe a risk this was perceived as in the risk and opportunity register, it is possible that scenario plans may even have been developed just for such an eventuality. So whilst it is something that the project team might wish they didn't have to deal with, they can manage it. Contrast this to a project with weak execution planning. For this project team the need for new wells will be a complete surprise and will lead to the emergence of unanticipated problems. For example the rig contract might not allow for an extension meaning that another rig must be found, or the additional drilling duration now means that there are unplanned simultaneous operations on a platform during hookup and commissioning, but there is insufficient persons on board capacity to handle this and it will be difficult to locate a floatel to accommodate the personnel. In short, the project quickly becomes a mess.

Execution Planning Rating (EPR) Sub-categories:

* **Schedule Development:**
* **Resource Planning:**
* **Risk and Opportunity Management:**
* **Project Control Plans:**
* **Scenario Planning:**
* **Discipline Integration:**

## Calculating The Project Definition Ratings

[calculation logic]

## Justification For The Pyrus Approach

An accusation that might be levelled against this approach to defining a project definition rating is that it is merely a rehash of existing approaches. And to be honest, any such accusation would be completely correct. It is a rehash. But it is a rehash that has been done, not with the intent of re-inventing the wheel, but to facilitate the functioning of an integrated project simulation environment.

In essence the shortcoming of existing measurement systems is that whilst they provide a good (and quick) view as to the level of definition for a project, they do very little to help understand what is driving that definition level. Where are the strengths and weaknesses? Whilst these can be uncovered through a more detailed analysis of the index components, such analysis is limited to identifying areas where the score could be improved. What it does not do is indicate which action taken to improve the project definition has the most influence on outcomes. In other words there may be several routes which would allow my project to achieve a _Best Practical_ FEL index, or a PDRI score of less than 200. Each of these routes will be associated with a different cost. But which route to choose? We would ideally prefer the route that delivers the most cost effective improvement, but I suspect that we might instead be forced to accept the cheapest route (or none at all).

Understanding which project definition elements have the most influence on outcomes is the aim of the Pyrus Suite project definition ratings. By using each element's definition rating to influence the distribution range of project inputs, Pyrus is able to determine which elements have definition that is more crucial to improve. In order to achieve this functionality, it was necessary to rehash the existing project definition approaches. In doing so Pyrus has a fit-for-purpose project definition approach that allows it to simulate the consequences that arise from taking different routes to improve project definition.