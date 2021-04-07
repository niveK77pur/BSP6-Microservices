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

# Does E4L allow for easy deployment of adjacent MSs?

The current Energy4Life application can be found at <https://juno.uni.lux/e4l>. Our first microservice had the job of obtaining data from E4L and plotting it in our Dash/Plotly python application.

## First attempt at obtaining data from the back-end

Upon inspection of the [E4L backend](https://minsky.uni.lu/gitlab/e4l/lu.uni.e4l.platform.api.dev/-/tree/master), we were able to find a potentially relevant endpoint that would allow us to [query answers from the questionnaire](https://minsky.uni.lu/gitlab/e4l/lu.uni.e4l.platform.api.dev/-/blob/master/src/main/java/lu/uni/e4l/platform/controller/QuestionnaireController.java#L49). However, it quickly became apparent that the back-end endpoints were specifically made to be used by the front-end page. Further evidence for this can be found in the [described functionalities](https://minsky.uni.lu/gitlab/e4l/lu.uni.e4l.platform.api.dev#project-funtionalities) in the README, which also informs us that the only data that can be extracted is specific to a session. All of this essentially means that we have no direct way to access any data beyond what we are presented on the web page&mdash;which would only comprise our results in the questionnaire.

Upon completing the questionnaire in E4L, we are presented the following page: <https://juno.uni.lux/e4l/result/MzA5.-7-sMYXmv3tXyuLQT2s1ZgULRKY>. The so-called *sessionId* is unique to our provided answers, so by taking note of it we are able to review our *energy score*s at any point in time. The obvious course of action now would be to use the `/calculate/session/{sessionId}` endpoint&mdash;which is our only of two GET handlers regarding the questionnaire&mdash;to obtain our results.

## Scraping the results page for data

One problem rose quickly however: We had no information on where or how to access the back-end in order to issue our GET request. Upon inspection in the code for both the front-end and back-end we were unable to find any concrete traces on how to reach it. The closest we managed to find was the definition of a [`baseUrl` using `axios`](https://minsky.uni.lu/gitlab/e4l/lu.uni.e4l.platform.frontend.dev/-/blob/master/src/js/container/info.js#L15), but that still left us clueless. So we set out to create a web scraper that would parse the HTML of the above results page.

We used `selenium` to perform this task. However, running the browser in headless mode&mdash;as was required for running this code in a docker container&mdash;caused issues with our scraper. It turns out that the page contents would not be loaded unless the page was rendered by the browser. In addition to this approach being extremely slow due to `selenium`, it was simply not usable for our use-case.

## Obtaining data from the back-end API

It was only after a third and final inspection of the E4L code&mdash;performed while documenting here what we did&mdash;with the help of GitLab's search utility, that we discovered how to [access the back-end](https://minsky.uni.lu/gitlab/e4l/lu.uni.e4l.platform.frontend.dev/-/blob/master/README.md#L19). It was hidden in plain site in the front-end README, however it was not highlighted very well which caused us to completely overlook it at multiple occasions.

That said, we were able to retrieve our results using the API by issuing a GET request on `https://juno.uni.lux/e4lapi/calculate/session/MzA5.-7-sMYXmv3tXyuLQT2s1ZgULRKY`. However, some data about global statistics (i.e. Luxembourg, Europe, World) were not available as they seemed to be hard coded into the front-end.

## Conclusion

The back-end endpoints allow us to easily access E4L functionality without being bound to the web page. This of course makes is very interesting to build adjacent microservices by directly requesting data through the exposed endpoints. To further improve this workflow, one would need to better highlight the existence and features of the back-end as they seem to be somewhat scattered throughout the application's documentations.

One should note however, that the endpoints were specifically made to be used by the front-end. This implies that some of the data obtained is not fully usable for *general* API usage (i.e. the hard coded data in the front-end). Further, as could be observed in the implementation of the back-end endpoints, not all available database and service functionalities were reachable through the API (i.e. no way to query **all** answers for making statistics). A tight coupling of front- and back-end can be observed here.

# Define assessment criteria to evaluate the benefits brought by MSs

What is the benchmark that allows us to conclude that architecting a web-based application as a collection of MSs is better than a monolithic system?
- Cycle time of the pipeline: time the pipeline takes to be completed.
- Lead time: from requirement to delivery.
- Time to restore:  how long does it take to restore to the latest well-known version one an failure has been detected?
- Scalability: how much time does it take to extend the existing services to support more requests (i.e. increase its capacity)?
- Any others?

# Methodology for Claims

Claims will be very technically devoted. As such, mere research on the matter will not suffice to validate them. While the research and methodologies presented for the RQs are relevant for this part, one should rather focus on the how to conduct the actual experiments that aim at validating a claim.

Seeing that claims are possibly independent, there is no single guide to be followed for each experiment. In case some claims rely on results of other claims, one should ensure that these were validated in the proper order, as otherwise the validity is questionable.

What is very important for the experiments, is that they are reproducible. As such, the experiments should be properly and explicitly documented while also presenting the obtained results and possibly the produced or used artifacts. Lastly, in order to prevent obscure experiments that lead to dubious conclusions one should design the experiments to be as small-scale and isolated as possible. The aim is to keep the experiments simple which in turn will lead to greater clarity and also better reproducability.