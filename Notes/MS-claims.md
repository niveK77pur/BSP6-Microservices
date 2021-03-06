---
title: Claims and Strategies for Proving
tags: scientific, MS
---

# Validate the following claims

[![hackmd-github-sync-badge](https://hackmd.io/hQ1DS3pvROSdNq-eAteNIA/badge)](https://hackmd.io/hQ1DS3pvROSdNq-eAteNIA?view)

## Deployability: a MS can be deployed independently from any other MS. Prove it.
If this is true, then it should be possible to:
    - add a new MSs to the collection of available MSs
    - do an independent replacement of a particular existing MS
## Reusability: a MS can be created using already existing MSs. This new MS represents a new "business service". Prove it.
- how do you ensure transactional properties over the the composite MSs?
- how do you deal with failures of any called MSs?
## Adaptability/Refactoring:
- how difficult is to refactor code to turn it MSs compliant?
- how difficult is to refactor code that is already compliant with the MSs style?

> The Guardian website is a good example of an application that was designed and built as a monolith, but has been evolving in a microservice direction. The monolith still is the core of the website, but they prefer to add new features by building microservices that use the monolith's API. This approach is particularly handy for features that are inherently temporary, such as specialized pages to handle a sporting event. Such a part of the website can quickly be put together using rapid development languages, and removed once the event is over. We've seen similar approaches at a financial institution where new services are added for a market opportunity and discarded after a few months or even weeks.  
<https://martinfowler.com/articles/microservices.html>

> There are certainly reasons why one might expect microservices to mature poorly. In any effort at componentization, success depends on how well the software fits into components. It's hard to figure out exactly where the component boundaries should lie. Evolutionary design recognizes the difficulties of getting boundaries right and thus the importance of it being easy to refactor them. But when your components are services with remote communications, then refactoring is much harder than with in-process libraries. Moving code is difficult across service boundaries, any interface changes need to be coordinated between participants, layers of backwards compatibility need to be added, and testing is made more complicated.
>
> Another issue is If the components do not compose cleanly, then all you are doing is shifting complexity from inside a component to the connections between components. Not just does this just move complexity around, it moves it to a place that's less explicit and harder to control. It's easy to think things are better when you are looking at the inside of a small, simple component, while missing messy connections between services.  
<https://martinfowler.com/articles/microservices.html>

## More to come (if you wish …)

> Often the true consequences of your architectural decisions are only evident several years after you made them. We have seen projects where a good team, with a strong desire for modularity, has built a monolithic architecture that has decayed over the years. Many people believe that such decay is less likely with microservices, since the service boundaries are explicit and hard to patch around. Yet until we see enough systems with enough age, we can't truly assess how microservice architectures mature.  
<https://martinfowler.com/articles/microservices.html>

> Finally, there is the factor of team skill. New techniques tend to be adopted by more skillful teams. But a technique that is more effective for a more skillful team isn't necessarily going to work for less skillful teams. We've seen plenty of cases of less skillful teams building messy monolithic architectures, but it takes time to see what happens when this kind of mess occurs with microservices. A poor team will always create a poor system - it's very hard to tell if microservices reduce the mess in this case or make it worse.  
<https://martinfowler.com/articles/microservices.html>

> One reasonable argument we've heard is that you shouldn't start with a microservices architecture. Instead begin with a monolith, keep it modular, and split it into microservices once the monolith becomes a problem. (Although this advice isn't ideal, since a good in-process interface is usually not a good service interface.)  
<https://martinfowler.com/articles/microservices.html>

# Define assessment criteria to evaluate the benefits brought by MSs
What is the benchmark that allows us to conclude that architecting a web-based application as a collection of MSs is better than a monolithic system?
- Cycle time of the pipeline: time the pipeline takes to be completed.
- Lead time: from requirement to delivery.
- Time to restore:  how long does it take to restore to the latest well-known version one an failure has been detected?
- Scalability: how much time does it take to extend the existing services to support more requests (i.e. increase its capacity)?
- Any others?

