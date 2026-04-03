---
layout: post
title: "We built a system to scale to thousands. It onboarded two users."
date: 2026-04-03 12:30:00 +0000
categories: Software
toc: false
---

 I have been ruminating a lot on a project I worked on some years ago. The company had come up with a new formula for financial products which they thought would sell. There was a new portal on what was then cutting edge servers capable of serving many thousands of clients. 

 We had been set an ambitious goal to create the client portal, and a generic content site. We went with an MVC site with a lightweight content management system called [N2CMS](https://www.n2cms.com/index.html). This allowed us to give the creative team the ability to complete the copy and images while we focused on the client portal. 

 In the end after months of effort, including some weekends of overtime getting things ready, the project went live. The team was exhausted. We pushed hard to get the content templated and the portal functionality completed before the launch. The launch went well and we celebrated the go live. Yet six months later I was tasked with decommissioning it. Only two users had been onboarded, one of whom logged in only at registration. This wasn't a technology failure, the project worked the site was fast, and responsive. Customers just weren't tempted with the offering.  

Architectural conversations often start with:
>“But will it scale?”

Which makes a certain amount of sense, it can be a lot of effort to rebuild a system and make it more scalable whilst also dealing with many users. The last thing you want is for users not to return after a bad experience with the site. However the better question might be:
>“Does anyone actually need this?”

Most systems don’t fail due to a lack of scalability. They fail because they never solve a real problem. As technologists it's often easier to think about the technology and making a great solution. We also need to worry about the bigger picture is this idea valid? Has it got a solid business case?

Years later it is clear to me that the business failed to validate the model, rushing to build a solution for a market that it turned out didn't exist. The technology team delivered the goods in terms of a working site, but they weren't able to deliver the users. Looking back we should have had more conversations on the realism of expectations. They expected to create a new product category without really testing it. Sometimes the bravest thing a team can do is ask the uncomfortable question before the first line of code is written.