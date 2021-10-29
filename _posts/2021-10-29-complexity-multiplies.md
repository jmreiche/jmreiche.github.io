---
layout: post
title: "Complexity multiplies cost"
date: 2021-10-28 06:46:04
categories: programming management
---

Complexity makes it harder to write the right code. Harder to test. Harder to document. Harder to host and run. Harder to bug-hunt and finally harder to maintain and change in the future.

In other words; complexity multiplies cost

The push for new and shiny solutions to old known problems ends up costing companies exorbitant amounts.

Of course there are scenarios where complex solutions are required, but they are few and far apart. Keep in mind that "complexity" can be relative. If everyone on the team is at home with CQRS and event sourcing then adding a new simple CRUD API might not be the right solution. Even though seen from a complexity standpoint it is "simpler". But then the team has two types of systems to maintain. That is _also_ additional complexity. It could be worth it - but do not just assume it is.

## Hardware over developers

Hardware can look very expensive. But throwing a team of developers at a performance problem that could be solved by hardware can out-pace that cost in a heartbeat.

Apply [Occam's razor](https://en.wikipedia.org/wiki/Occam%27s_razor) and deduce whether a simpler solution to the problem exists. It is easier to add complexity to a simple solution than trying to remove it from a complex one.

## Team capabilities

If only one developer really understand the solution or the code - then it is not the right solution for that team. It will be a very expensive short term win.

Is the solution worth sending the entire team off for education and then have them build, host and maintain a system or technology they only just heard about?

This is of course not to talk against education of employees or teams, but about selecting the right solution for the problem. Developers should be allowed to play and be creative, but it should be in proportion to the criticality of the system and potential long term cost.

## Requirements are rarely so.

Always look for the simplest solution to a problem, not the most hyped. If a requirement can only be solved by a complex solution give push-back on the requirement and make sure that it is not possible to solve the requirement by other means, only solve 90% of it or perhaps dodge it altogether.

As anyone who has ever written any code knows, there are multiple ways to solve a problem. Rarely do you see only one "right" way to solve it.

The same logic applies to requirements.

If we take a basic example; there is either a "tender" that "dictates" end goals for a product or a Product Owner/Team that wants a new feature. In order to do so someone formulates a requirement that describes what is "needed". This description is then handed over to an architect or developer that either designs or implements the feature. As mentioned earlier the implementation can vary and still fulfill the requirement. There is not one single solution.

But we can also go back a step and look at the business requirement. Is that the only way to fulfill the tender or deliver the feature needed? It rarely is.

Business people and developers are notoriously challenged in communicating. But when developers see requirements that can only lead to complex solutions then it should be prime candidate for another round with the business analysts to see whether the requirement can be changed.

All levels and parts of an IT project are plastic, meaning they can be molded or altered. A good IT project consists of people at all levels being prepared to change their mind and look for simpler solutions.
