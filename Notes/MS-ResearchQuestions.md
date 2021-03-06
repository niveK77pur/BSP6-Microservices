---
title: Research Questions
tags: scientific, MS
---

[![hackmd-github-sync-badge](https://hackmd.io/G1fp9nwgSh22kN0f_lhh7g/badge)](https://hackmd.io/G1fp9nwgSh22kN0f_lhh7g?view)


[^ms-definition]: *Microservices: A definition of this new architectural term*  
https://martinfowler.com/articles/microservices.html


# What is a microservice (MSs)?

A first simple definition can be found in the first paragraph of the MSs article[^ms-definition] that lays out some aspects which characterize a MS architecture.

> In short, the microservice architectural style is an approach to developing a single application as a **suite of small services**, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API. These services are **built around business capabilities and independently deployable** by fully automated deployment machinery. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies. 

However, as stated multiple times in the article[^ms-definition], there is no formal definition for MS. As such, we shall inspect characteristics that such architectures tend to have in common.

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

Monolithic applications usually prefer to have a single logical database for persistent data, which in turn are then often used across a range of applications. Microservices on the other hand let each serivce manage its own database. Both are illustrated in the following figure.

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

Monolithic architectures build applications using a single unit. This basically means that all of an application's functionality, logic, classes, function and namespaces are in the end located within a single executable and thus run within a single process.

This model works in fact and allows us to create a functioning system, but still raises some issues. For instance, the fact that even the smallest of changes within an application require the whole product to be rebuilt and redeployed is not an ideal scenario.

As the application's life cycles goes on, it is unavoidable to modify it over time. In a monolithic system however, it becomes increasingly difficult to maintain a modular structure&mdash;if one was present to begin with&mdash;which in turn also leads to modifications becoming more expensive. This is explained by the fact that if a modular structure cannot be guaranteed, it possibly entails that changes to one component may impact other components which were not supposed to be affected.

Lastly, if we want to scale an application we actually need to scale the application as a whole rather than the individual components that need to be scaled.

![Monoliths and Microservices](https://martinfowler.com/articles/microservices/images/sketch.png)

The figure above nicely demonstrates some of the mentioned issues and how MS architecture tries to tackle them.

Since the application is now split into separate services, making modifications will not require us to built and deploy the whole application anew. It will be enough to only update the serves in question.

The fact that the application is built in a modular fashion from the ground up and divided into smaller chunks and services, makes it easier to maintain a modular structure within the application as a whole. As a result, making changes to one such service will guarantee that none of the other services will be affected as well as keep undesired or unforeseen side effects within a given service to a minimum.

We can also see that scaling an application becomes much less of a burden. No longer do we need to scale the whole application, but we can simply introduce new services where needed.

*Interesting piece of information*
>We do not claim that the microservice style is novel or innovative, its roots go back at least to the design principles of Unix.


## Are there any patterns? See book to be provided as reference.

### Find out what is the best pattern to be used in the context of E4L.

### Is the current E4L's architecture (i.e. front-end + back-end) a MS's pattern?

### If not, explain why and how it should the current architecture be adapted to adhere to a MS's pattern.

## What is the role of MS in DevOps?

## Why MS are important in the context of DevOps?

## How are both concepts connected to each other?

## What is the standard manner of modelling an architecture, and in particular how can we model MSs?

## What is the best kind of request for a particular MS between POST and GET?

### What is a POST request?

### What is a GET request?

### Can you provide a rationale to help making the decision?
