---
layout: post
title: "Impact Analysis for Software Architects"
date: 2023-04-10 09:00:00 +0300
category: architecture
tags: 
  - architecture
  - toolkit

---
Software changes are inevitable. Business stakeholders and product owners don't question this assertion, and software developers and architects accept this fact. New features, regulatory changes, security issues, and bug fixes generate an endless flow of new functional and non-functional requirements. Typically, agile methodologies provide frameworks that embrace changes in software requirements and support turning them into opportunities for business. However, the actual implementation of changes into tested software is still a risky and costly process. It requires various skills from an architect to identify, assess and communicate an impact of a change to business stakeholders and the development team. An approach that can help a software architect to conduct the change management process in a structured way is impact analysis.<!--more--> 

# What is Impact Analysis?

**Impact analysis** is the process to identify, analyze and evaluate the effect of a change to the software system and the project.

A software system can change for multiple reasons:


| Change Reason           | Change Origin |
| ----------------------- | ------------- |
| New user needs          | This is one of the most common reasons for a modification. Most often, they originate from changes in the market or changes in user preferences |
| Bugs in functionality   | Issues related to functional or non-functional parts of the software system |
| Regulatory compliance   | Changes in rules and regulations issued by the government that are mandatory to follow. Examples are GDPR in the EU, HIPAA in the US, and PCI DSS worldwide  |
| New project opportunities | These are the changes that originated from inside the project due to the discovery of new information or knowledge. They could be improvements in architectural characteristics or functionality or process changes. It could also be any unforeseen complications to functionality or architecture |
| Underlying hardware and operating system changes | Software systems can migrate to new hardware; upgrade to a new version of the operating system, or change it completely. These changes may be caused by the evolution of a business direction that the software system supports or the evolution of underlying hardware and operating system that the software system depends on |
| Business reorganization | Changes that originate from significant changes in business: mergers and acquisitions, shifts in organizational strategy, etc. |
| Project limitations     | The project constraints for available budget, schedule, or resources could change over time which usually affects project scope and causes changes to the software system |

At the same time, a single change can affect multiple aspects of a software system:

| Affected Aspect | Notes |
| --------------- | ----------- |
| Requirements  | They are affected by changes in functionality requested by various project stakeholders. Usually, they are a natural part of the modern software development process |
| Architecture  | Changes in non-functional aspects of the software system that affect the system foundation. As a result, it may lead to timely and costly modifications |
| Project constraints | Changes in the direction of business development affect project schedule, budget, resources, and the scope |
| Project risk factors | A change might affect technical, business,  project management, or personnel risks. |

The goal of impact analysis is to support the change management process by giving the answers to the following questions:
* What are the driving factors of the change?
* Which stakeholders will be affected by the change and how?
* What are the expectations of the most important stakeholders for the resources needed for change implementation?
* When would stakeholders like to implement the change? When could the team implement the change?
* Does the team have the complete information to implement the requested change? Does the team need to elaborate on any enabling stories before the implementation of the requested change?
* Which features of the system are affected by the change?
* Which components of the system are affected by the change?
* Which architectural characteristics are impacted and how?
* What is the scope for regression testing?
* What are the options to develop the requested change in the system? What is the most effective option by the trade-offs?
* What are the risks of change implementation?
* Are there any dependencies of the requested change to other features on the roadmap?
* How does the implementation of the change affect the project roadmap? Does the change affect the project’s critical path?
* Does the team create or resolve any technical debts related to the change requested?

The core advantages of impact analysis are:
* Increasing transparency of relationships and strengthening trust with your customer.
* Minimization of potential loss of time and budget.
* Minimization of potential risks of implementation of the change.
* Keeping software quality and user satisfaction high.

# How to Conduct Impact Analysis
An architect can conduct impact analysis themselves for simple change requests or as a part of the development team if the change affects multiple dimensions of the complex system. In the latter case, the architect invites to the discussion the most relevant team members: product owners, subject matter experts, development leads, QA leads, and the most experienced developers and QA engineers with related knowledge of the system.

The team discussion can take the form of a full-time online meeting or an offline analysis first and an online synthesis of the results next.

In general, the architect can perform the following steps to organize the efficient impact analysis process:

| #   | Step                      | Description |
| --- | ------------------------- | ----------- |
| 1   | Define the change         | Collect all the information you know about the change and present it in a compact form which you then share with the team. Identify the high-level scope and objectives of the change request. The goal is to create a common ground for further discussions with the team |
| 2   | Define the team structure | Find out which stakeholders should be a part of the impact analysis team. It is usually the required step to analyze comprehensive changes or changes in a complex software system. The architect should keep the number of participants as minimum as possible. Also, the architect can analyze simple changes or expected changes in well-known parts of the system on their own |
| 3   | Prepare a questionnaire     | Write the goal of the analysis and the questions to answer as a questionnaire. Such visualization guides the team through the process and improves the efficiency of the analysis procedure |
| 4   | Get the team together     | Meet with the team and discuss the goal and the expected results of the analysis |
| 5   | Perform analysis          | Analyze the impact of the change on an online or offline meeting. The team can perform the analysis together, or each participant can work themselves. During the analysis, the team should examine the impact of the change to software components, internal and public communication interfaces, architectural characteristics, tests, and documentation. The team should also work out an understanding of the impact on user experience and the work of relevant stakeholders |
| 6   | Evaluate the results      | Evaluate the results collected during the analysis step, identify risks and technical debts (if any), and synthesize recommendations for the change implementation and testing. The team should evaluate possible options to implement the change and propose one that balances the implementation trade-offs and risks. The team should also elaborate on strategies to mitigate risks identified during this step. The selected option should find its place on the project roadmap. The team should document all discovered risks, trade-offs, and assumptions |
| 7   | Report findings           | Collect all the results and findings in the [impact analysis report](impact-analysis-for-architects.html#how-to-document-results-of-impact-analysis) |
| 8   | Share the report          | Communicate and validate the results of impact analysis with the relevant stakeholders |

# How to Document Results of Impact Analysis

The impact analysis should result in the impact analysis report. It can take different forms but should include the minimum set of the following sections:


| #   | Section           | Content |
| --- | ----------------- | ------- |
| 1   | Overview          | Briefly describe the software change request, its purpose, and scope |
| 2   | Stakeholders      | Identify the stakeholders who are positively and negatively affected by the change and their expectations |
| 3   | Implementation    | Briefly describe the selected implementation option. Specify affected functionality, system components, architectural characteristics, communication interfaces, and documentation. Mention alternative implementation options, if any. Describe the main reasons you selected the implementation option among other alternatives. Provide recommendations for testing the changed functionality. Identify changes in time, budget, and roadmap |
| 4   | Assumptions       | List all knowledge gaps about the change and provide reasonable assumptions about them. Identify any prior activities that should enable the implementation of the change. The information in this section will be subject to further clarification with relevant stakeholders |
| 5   | Impacted Artifacts | System components, communication interfaces, documentation, roadmap, time, budget, resources |
| 6   | Risks            | List all risks identified during the analysis and result evaluation steps. Elaborate on mitigation strategies. Describe the potential challenges of change implementation. Identify any technical debts that will be produced or resolved as a result of the development |
| 7   | Conclusion        | Summarize the impact analysis report with clear and concise recommendations to implement the change. Pay attention to changes in timeline, budget, and roadmap. Highlight the critical risks and assumptions |

The impact analysis report could take different forms. It could be a formal Word or Google Docs document with a title page, contents, and sections in the styles of the organization. It could also take a form of a note as an [Architecture Decision Record](https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/welcome.html){:target="_blank"}. The team selects the option that works for them the best. As a rule of thumb, the architect should prefer less formal Architecture Decision Records over any other formats as it usually works better for the development team.

# Conclusions
Changes are inevitable. So the architect and the team should embrace the changes. They should proactively manage and coordinate them. Any change request and a change management activity should start from the analysis of the impact of the change. The quality of the outcomes of all subsequent steps of the change management process depends on the quality of the impact analysis.

Poorly managed changes usually trigger critical project risks. Triggered risks reinforce tension in the development team and between the development team and business stakeholders. It amplifies team attrition and disappointment in the team leadership. In its turn, it creates times that call for heroes inside the development team, which usually don't last long and have a catastrophic impact on the project.

The ability of the architect and the development team to systematically respond to the software and process changes and reasonably manage them by minimizing their negative impact is an important skill. It assists in reducing the gaps and building trust between the business stakeholders, subject matter experts, and the development team. It helps to keep the team's health and spirit at a high level.
