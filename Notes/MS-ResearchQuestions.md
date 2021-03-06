---
title: Research Questions
tags: scientific, MS
---

[![hackmd-github-sync-badge](https://hackmd.io/G1fp9nwgSh22kN0f_lhh7g/badge)](https://hackmd.io/G1fp9nwgSh22kN0f_lhh7g?view)


[^ms-definition]: *Microservices: A definition of this new architectural term*  
<https://martinfowler.com/articles/microservices.html>


# What is a microservice (MSs)?

A first simple definition can be found in the first paragraph of the MSs article[^ms-definition] that lays out some aspects which characterize a MS architecture.

> In short, the microservice architectural style is an approach to developing a single application as a **suite of small services**, each running **in its own process** and communicating with lightweight mechanisms, often an HTTP resource API. These services are **built around business capabilities** and **independently deployable** by fully automated deployment machinery. There is a **bare minimum of centralized management** of these services, which may be written in different programming languages and use different data storage technologies. 

However, as stated multiple times in the article[^ms-definition], there is no formal definition for MS. As such, we shall inspect characteristics that such architectures tend to have in common.

Before we move on, an interesting point to note here is the decreased need for *centralized management*. [To give an example](https://content.personalfinancelab.com/finance-knowledge/management/centralized-and-decentralized-management-explained/?v=c4782f5abe5c), centralized management is a structure where only a few individuals make most of the decisions in a company. We can find hierarchies within such a structure, each having to respond to the superior's communicate. 

Decentralized management on the other hand would for example allow a manager at a call center or retail store to make instant decisions that impact their work environment. Thus, the decisions are not taken solely by the higher ups but responsibilities are spread across sectors—which could further spread responsibilities. This is an interesting analogy to keep in mind for the following.

## Componentization via Services

The article distinguishes between two types of components (where *component* refers to *a unit of software that is independently replaceable and upgradeable*):
- *libraries* which are being linked into a program and are called using in-memory function calls; and
- *services* which are external components where communications happen with mechanisms such as web service requests or remote procedure calls.

The advantage of *services* is that such components can be deployed independently&mdash;which is not the case with *libraries* because then a component is essentially directly integrated into another component. It should be noted that some service component modifications may affect its interface, which in turn will require other service components to be adjusted accordingly. The aim however is to minimize these through cohesive service boundaries and evolution mechanisms in the service contracts.

Another advantage of services is a more explicit component interface. Often it's only documentation and discipline that prevents clients from breaking a component's encapsulation, thus leading to overly-tight coupling between components. Services make it easier to avoid this by using explicit remote call mechanisms.

## Organized around Business Capabilities

Traditionally, splitting a large application into parts is done by assigning a specialized team to each layer of the application, as illustrated in the following figure. This distribution of work force eventually leads to each team worrying only about their specific task instead of the application as a whole, which is an example of *Conway's Law*:  
> Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization's communication structure.  
&mdash; Melvin Conway, 1968

![Conway's Law in action](https://martinfowler.com/articles/microservices/images/conways-law.png)

In regards to MS, we divide the work force according to *business capabilities* rather than application layers. This implies that our resulting team will be [cross-functional](https://en.wikipedia.org/wiki/Cross-functional_team), combining the full range of skills required to develop the service in question. This division leads us to the following figure.

![Service boundaries reinforced by team boundaries](https://martinfowler.com/articles/microservices/images/PreferFunctionalStaffOrganization.png)

> Large monolithic applications can always be modularized around business capabilities too, although that's not the common case.

The issue is that monoliths tend to span over too many contexts or modules, which makes it difficult for developers to fit the various parts into respective modules.

> Additionally we see that the modular lines require a great deal of discipline to enforce. The necessarily more explicit separation required by service components makes it easier to keep the team boundaries clear.

## Products not Projects

> Most application development efforts that we see use a project model: where the aim is to deliver some piece of software which is then considered to be completed. On completion the software is handed over to a maintenance organization and the project team that built it is disbanded.
>
> Microservice proponents tend to avoid this model, preferring instead the notion that a team should own a product over its full lifetime. [&#8230;] The product mentality, ties in with the linkage to business capabilities. Rather than looking at the software as a set of functionality to be completed, there is an on-going relationship where the question is how can software assist its users to enhance the business capability.

## Smart endpoints and dumb pipes

Often times when it comes to process communication, great importance is put on the communication mechanism itself. With regards to microservices however, we place greater importance on the endpoints rather than the pipes.

> Applications built from microservices aim to be as decoupled and as cohesive as possible - they own their own domain logic and act more as filters in the classical Unix sense - receiving a request, applying logic as appropriate and producing a response. These are choreographed using simple RESTish protocols rather than complex protocols such as WS-Choreography or BPEL or orchestration by a central tool.

All of this further highlights that services in a microservice architecture are to be isolated components. When services need to communicate, the magic happens on the services' endpoints and not in the message bus&mdash;in fact we need no more for messages than simply being transferred from one endpoint to the other.

*Interesting piece of information:*
> In a monolith, the components are executing in-process and communication between them is via either method invocation or function call. The biggest issue in changing a monolith into microservices lies in changing the communication pattern. A naive conversion from in-memory method calls to RPC leads to chatty communications which don't perform well. Instead you need to replace the fine-grained communication with a coarser -grained approach.

## Decentralized Governance

Centralized governance that often comes along with monolithic architectures have the tendency to standardize on single technology platforms, which can be restricting. Rarely can a single language full potential be leveraged for the entirety of an application.

Splitting our application into services however, we do have the ability and choice of building each service with whatever tool is best for the job.

This approach also brings a different mindset into development. Instead of focusing solely on defined standards for a product, developers prefer the idea of creating useful tools that can potentially also be used by other developers to solve similar problems.

## Decentralized Data Management

Monolithic applications usually prefer to have a single logical database for persistent data, which in turn are then often used across a range of applications. Microservices on the other hand let each service manage its own database. Both are illustrated in the following figure.

![Monolith and Microservice Databases](https://martinfowler.com/articles/microservices/images/decentralised-data.png)

The issue that arises with decentralizing the data is consistency. Managing this is in fact not an easy task and usually comes at the cost of increased computing times, leading to *eventual consistency* and *compensation operations* being the preferred method.

> Often businesses handle a degree of inconsistency in order to respond quickly to demand, while having some kind of reversal process to deal with mistakes. The trade-off is worth it as long as the cost of fixing mistakes is less than the cost of lost business under greater consistency.

## Infrastructure Automation

> Many of the products or systems being build with microservices are being built by teams with extensive experience of Continuous Delivery and it's precursor, Continuous Integration. Teams building software this way make extensive use of infrastructure automation techniques. This is illustrated in the build pipeline shown below.

![basic build pipeline](https://martinfowler.com/articles/microservices/images/basic-pipeline.png)

> Another area where we see teams using extensive infrastructure automation is when managing microservices in production. In contrast to our assertion above that as long as deployment is boring there isn't that much difference between monoliths and microservices, the operational landscape for each can be strikingly different.

![Module deployment often differs](https://martinfowler.com/articles/microservices/images/micro-deployment.png)

## Design for failure

One needs to take into consideration that a service may fail at any point in time for whatever reason, and the client should respond to this as gracefully as possible. Due to the decoupled nature of MS, we have an additional complexity to take into account which constantly requires us to reflect on how service failure can affect the user experience.

It is therefore of utmost importance to detect failures quickly and restore the service. To achieve this, a lot of sophisticated monitoring and logging on various parts of a service are performed.

> Microservice applications put a lot of emphasis on real-time monitoring of the application, checking both architectural elements (how many requests per second is the database getting) and business relevant metrics (such as how many orders per minute are received). Semantic monitoring can provide an early warning system of something going wrong that triggers development teams to follow up and investigate.

## Evolutionary Design

<span style=color:aqua>Not sure I fully understood the point that was made here</span>

- with the right attitudes and tools you can make frequent, fast, and well-controlled changes to software
- breaking software into components: where and how do you divide the pieces?
    - The key property of a component is the notion of independent replacement and upgradeability, i.e. points where rewriting a component does not affect other components.
- keep things that change *at the same time* in *the same module*
    - Parts of a system that change rarely should be in different services to those that are currently undergoing lots of churn.
- components as services allows for more granular release planning
    - monolith: full rebuilt and deployment of entire application
    - MS: redeploy modified services only &rarr; simply & speed up release process
    - downside: service changes causing issues on consumers
    - avoid versioning by designing services to be as tolerant as possible to changes in their suppliers.


# Motivation behind MS architecture

To understand the benefits and motivations behind using MS over the monolithic style, we will first look at the latter.[^ms-definition]

Monolithic architectures build applications using a single unit. This basically means that all of an application's functionality, logic, classes, function and namespaces are in the end located within a single executable and thus run within a single process. This model works in fact and allows us to create a functioning system, but still raises some issues. For instance, the fact that even the smallest of changes within an application require the whole product to be rebuilt and redeployed is not an ideal scenario.

As the application's life cycles goes on, it is unavoidable to modify it over time. In a monolithic system however, it becomes increasingly difficult to maintain a modular structure&mdash;if one was present to begin with&mdash;which in turn also leads to modifications becoming more expensive. This is explained by the fact that if a modular structure cannot be guaranteed, it possibly entails that changes to one component may impact other components which were not supposed to be affected.

Lastly, if we want to scale an application we actually need to scale the application as a whole rather than the individual components that need to be scaled.

![Monoliths and Microservices](https://martinfowler.com/articles/microservices/images/sketch.png)

The figure above nicely demonstrates some of the mentioned issues and how MS architecture tries to tackle them.

Since the application is now split into separate services, making modifications will not require us to built and deploy the whole application anew. It will be enough to only update the serves in question.

The fact that the application is built in a modular fashion from the ground up and divided into smaller chunks and services, makes it easier to maintain a modular structure within the application as a whole. As a result, making changes to one such service will guarantee that none of the other services will be affected as well as keep undesired or unforeseen side effects within a given service to a minimum.

We can also see that scaling an application becomes much less of a burden. No longer do we need to scale the whole application, but we can simply introduce new services where needed.

*Interesting piece of information*
>We do not claim that the microservice style is novel or innovative, its roots go back at least to the design principles of Unix.

# Drawbacks of MS

Given all the above, there are drawbacks which come with the microservice architecture. As already pointed out [previously](#design-for-failure), a service can fail at any point in time which makes it quite difficult to build distributed systems. Added to this, remote call are slow which can also have an impact of the final product's performance. Another drawback that we discussed previously is the issue of [eventual consistency](#decentralized-data-management).

As slightly touched upon in the [infractructure automation](#infrastructure-automation) section, we will run into the issue of [**operational complexity**](https://martinfowler.com/articles/microservice-trade-offs.html#ops), demanding a mature operations team to manage lots of services, which are being redeployed regularly. A product consisting of half-a-dozen applications can quickly expand into hundreds of little microservices. Here, the [role of DevOps](#what-is-the-role-of-ms-in-devops) comes into play to ease this whole process. It becomes increasingly harder, tedious and eventually impossible to manage an increasing number of services which also calls for a lot of automation and monitoring to take place. Given that MSs are smaller and thus easier to understand, problems are likely to occur in the interconnections of such services which potentially makes it very difficult to trace and debug issues&mdash;hence the need for proper and elaborate monitoring.

# Are there any patterns? See book to be provided as reference.

Maybe useful references:

- <https://martinfowler.com/articles/break-monolith-into-microservices.html>
- <https://martinfowler.com/articles/extract-data-rich-service.html>

## Find out what is the best pattern to be used in the context of E4L.

## Is the current E4L's architecture (i.e. front-end + back-end) a MS's pattern?

## If not, explain why and how it should the current architecture be adapted to adhere to a MS's pattern.

# What is the role of DevOps in MS?

*NOTE: Discussion found in [section blow](#how-are-both-concepts-connected-to-each-other)*

- <https://martinfowler.com/articles/microservice-trade-offs.html#ops>

  Operational Complexity. Refer to [previous section](#drawbacks-of-ms).

- <https://martinfowler.com/bliki/MicroservicePrerequisites.html>

  talks about things that need to be taken into consideration and why they bear more importance than for monolithic architectures

  - **Rapid provisioning.** To be able to rapidly launch a server (within a few hours), it is important to automate the process of provisioning. To seriously make use MSs, one should strive for a fully automated process&mdash;as far as this is possible.

  - **Basic monitoring.** Due to loosely-coupled services working together in production, things are bound to go wrong in ways that are difficult to detect in test environments. It is therefore of utmost importance to ensure serious problems&mdash;appearing through technical issues, but also business issues&mdash;are detected swiftly.

    *Technical issues* could encompass run-time errors, or service availability. *Business issues* can be related to a drop in orders, or problems with sending transactions through multiple services.

  - **Rapid application deployment.** Deployment usually involves a [pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html) with various steps, each providing some sort of validation of the product. With many services to manage, this pipeline should striven to be fully automated. Achieving this implies close collaboration between developers and operations: the **[DevOpsCulture](https://martinfowler.com/bliki/DevOpsCulture.html)**.

- <https://martinfowler.com/bliki/DevOpsCulture.html>

  - Traditionally software development is broken down into distinct categories such as requirements analysis, testing and development as well as deployment, operations and maintenance once the product is released. DevOps aims that merging these categories and favour collaboration between **dev**elopment and **op**eration**s**.
  - A DevOps approach has the side effect of *shared responsibilities*, encouraging closer collaboration. As briefly mentioned [previously](#organized-around-business-capabilities), since developers and operators are now working under the same hood, the former is much more inclined to adapt a mindset of finding ways to simplify deployment and maintenance since they are closely involved with the tasks of the operators. If the development team would just hand over the finished product and not worry about it anymore, they would not see the difficulties operators might need to face in light of their productions.
  - Given that each party is more involved in each other's tasks, this leads to better understanding of the processes and issues which developers and operators need to tackle. This allows for much more efficient problem solving and leads to teams needing to value building quality into the development process. The aim being to automate the deployments and seeding up the testing cycle which results in greater ease of putting code into production.
  - Automation is an important factor in all of the above. For one, automated tasks such as testing, configuration and deployment free staff of these burdens and reduce human error. Another point is that the automation scripts essentially serve as useful and up-to-date documentation of the system. Should, for example, a developer or operator want to inspect or change how a server is configured, they know where to look.

# Why MS are important in the context of DevOps?

# How are both concepts connected to each other?

Given the above points and a few of the characteristics of MSs, one could derive the implication that DevOps is, in fact, an inherent aspect that comes along with the microservice philosophy. The two points that lead us to this assumption were the [Organized around Business Capabilities](#organized-around-business-capabilities) and [Infrastructure Automation](#infrastructure-automation) characteristics.

The [infrastructure automation](#infrastructure-automation) becomes increasingly relevant if our network of MSs expands. Managing only a handful of services by hand can still be feasible, but if we have a few dozens or more services this quickly becomes a virtually impossible task as we would need to take care of each of the countless services individually. As for the DevOps side of things, to increase production speed automation is an equally important factor. Not only does it free the staff of work, but it also reduces human error&mdash;which seems likely to seep in when managing lots of services in parallel. These viewpoints on automation allow us to draw a conclusion on how the DevOps approach is vital in order to make working with microservices a feasible task.

The second point is that the automation scripts serve as useful and up-to-date documentation of the system. This is very useful in that both developers and operators can inspect these scripts and be on the same page with regards to how the system is built and works. This directly leads us to the MS characteristic of [organizing around business bapabilities](#organized-around-business-capabilities). The fact that we do not split our teams according to application layers&mdash;as is usually the case for monoliths&mdash;but rather according to functionalities, favours collaboration of each application layer's expert. The automation scripts coming forth through the DevOps aspect serve in this regard as a bridge to ease this collaboration among team members and thus help further boost and encourage the idea of organising in terms of functionalities rather than isolated application layers.

*__Threats to validity:__ The above discussion and conclusions have been drawn without knowledge on the technologies. Merely the researched material presented from the start of this document up until this point were taken into consideration. That said, given more expertise on how it works in practice might give a more precise and elaborate answer.*

Parallels of GitLab CI/CD to aspects of DevOps and MS

# 1 - How can DevOps aspects be implemented using/in GitLab CI/CD?

Here we will look at what parts of the GitLab tools are related to DevOps and MS aspects&mdash;which were mentioned in the [Research Question][rqdoc] document.

[rqdoc]: MS-ResearchQuestions

## Automation (build & deploy)

As pointed out in the [DevOps section](MS-ResearchQuestions#what-is-the-role-of-ms-in-devops?) of the [research document][rqdoc], automation plays a big role in DevOps and MS. In GitLab we have the ability to create so-called [*pipelines*](GitLab-CICD#what-are-pipelines?) which handle certain tasks automatically. A pipeline essentially consists of a collection of jobs which are executed at specified stages of the pipeline. Within a job we have&mdash;among many others&mdash;the ability to specify what commands should be run.

Having these commands written out explicitly in this manner first of all allows us to simply run the job&mdash;which in turn will execute the provided instructions&mdash;and have our application be automatically built, tested, deployed or complete whatever task our job is aimed at. As a matter of fact the pipeline, along with its jobs, is run after every push to the repository. Hence we only merely need to change the code and GitLab will take the rest of the work off our shoulders.

## Developer and Operator Collaboration

Another point that was made in the [research document][rqdoc] is that these automation scripts serve as a common reference for every member of a team. And indeed, viewing the GitLab pipeline specification is not restricted solely to a subset of the team. Everyone can see it and glimpse at how the application is built and what stages need to be completed before it gets the green light into production.

In case the pipeline needs a modification, either due to issues or other reasons, it is not simply the role of the operators to take care of handling and solving the situation. If for example the developers need something to change in the pipeline, they can consult directly and in a precise manner with the operators on what would be the best course of action, or maybe they could even implement the modifications themselves directly.

## Shared responsibilities

This point also directly leads us to the briefly mentioned shared responsibilities in the [research document][rqdoc]. Each member of the team is quite closely involved with creating the pipeline. For instance the developers should specify how the the application is to be built; testers should take care of how tests are to be executed; operators handle everything with regards to deploying the product; and so on

Since the whole pipeline is defined within a single configuration file on GitLab, every member of the team can see the role and work of the others and hopefully becomes more inclined to simplify the whole process. In contrast to a monolithic approach where the developer's product is simply handed over to the operators and not looked after anymore by its creators, here everyone is fully engaged with every step of the process. If for instance a developer causes the pipeline to fail, for whatever reason, the whole process will be on halt until solved&mdash;because the code cannot be deployed if previous stages like building or testing have failed.

*__Methodology__: Answering this RQ was possible through the accumulation of answers in sections prior to this one, and by having a look at the [GitLab CI/CD workflow](GitLab-CICD). The RQ provided us with the basic knowledge required to understand the topic at hand, while the GitLab tutorial in particular gave us the first dive into the technology. Both lead us to answering this RQ.*

*__Threats to validity:__ Very little knowledge on the technologies was present at the time of writing these correlations. In fact, the introductory GitLab tutorial brought the only experience. That said, some of the information may be incomplete or imprecise due to a lack of expertise.*

**Methodology:** (0) Answer question *What is the relationship between DevOps and MSs?* (1) use the in-house hosted of gitlab service provided by the university. Request access to this service. (2) Create a repository where we store our application. Expected to along E4L app. (3) Create a pipeline to automate the build and deploy of app in a testing environment.

Relation between DevOps & MS?

# How can MS aspects be implemented using/in Docker?

# What is the standard manner of modelling an architecture, and in particular how can we model MSs?

# What is the best kind of request for a particular MS between POST and GET?

## What is a POST request?

## What is a GET request?

## Can you provide a rationale to help making the decision?



# Methodology for RQ

## Types of RQs presented

We explore two main types of RQs in this project. The first type focuses on general questions regarding the technologies and concepts. Within this type of RQs we have one particular question that may require a different methodology.

This last RQ leads us to the second type, focusing on more practical use cases. These questions will rather explore the features of the technologies as well as their implications on actual productions. This second type partly corresponds to our *claims* which need to be validated using a certain procedure.

## Methodology for first type of RQ

Given that these questions will focus on general ideas and concepts regarding MSs and their technologies, collecting answers will not be conducted using experiments. Answering these RQs will be done through research on the given topics as well as drawing our own conclusions based on the collected material and insights.

We shall present and structure the questions in a way that their answers builds upon each other. The goal is to introduce various concepts such that we can lay out our line of thought and reach conclusions for other RQs.

## Methodology for second type of RQ

The more technically inclined RQ will require a bit more than just research. The insights gained from the previous type of RQ shall however be of great value for obtaining our answers. Here we will need a more practical approach, inspecting the actual tools and applications, in order to makes ties between the technical parts and the concepts. This combination is what will allow us to answer with a high degree of certainty.

To give a concrete example, let's consider the question *Is the current E4L's architecture (i.e. front-end + back-end) a MS's pattern?*. We clearly need to be familiar with the concepts that surround MSs patterns in order to answer this question. However, we furthermore need to inspect the E4L application itself and draw parallels which will yield our answers.

Regarding the methodology for claims, please refer to the [documents treating the claims](MS-claims#methodology-for-claims).