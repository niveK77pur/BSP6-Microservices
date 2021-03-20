---
title: GitLab CI/CD
tags: technical, gitlab
---



[gl-cicd]: https://docs.gitlab.com/ee/ci/
[gl-pipelines]: https://docs.gitlab.com/ee/ci/pipelines/index.html
[gl-pipearch]: https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html
[gl-ciguide]: https://docs.gitlab.com/ee/user/project/pages/getting_started/pages_from_scratch.html
[gl-registry]: https://docs.gitlab.com/ee/user/packages/container_registry/
[d-registry-intro]: https://docs.docker.com/registry/introduction/
[gl-docker]: https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-the-docker-executor-with-the-docker-image-docker-in-docker

# What is CI/CD?

*Continuous Integration* (CI) basically allows us to run a set of scripts upon pushing onto a repository. These scripts can build, test and validate the changes before we proceed to merge them onto the main branch.

*Continuous Delivery* and *Deployment* (CD) takes CI a step further by also deploying your application to production at every push to the default branch of the repository.

The goal behind CI/CD is to catch bugs and errors as early as possible while ensuring that all the code deployed to production complies with the code standards you established for the application. *Collected from [GitLab docs][gl-cicd].*

# GitLab Introduction

The following video provides a quick overview at how CI/CD works in action. If you are unfamiliar with how it looks in practice, this is a great place to start.

{%youtube 1iXFbchozdY %}

The [main page on CI/CD][gl-cicd] has a *getting started* section which further presents four documents, each aiming to help you understand and work with GitLab CI/CD.

## What are pipelines?

> Pipelines consist of one or more stages that run in order and can each contain one or more jobs that run in parallel. These jobs (or scripts) get executed by the [GitLab Runner](https://docs.gitlab.com/runner/) agent.

The following has been taken from the [GitLab pipelines docs][gl-pipelines].

> Pipelines are the top-level component of continuous integration, delivery, and deployment.
>
> Pipelines comprise:
>
> - Jobs, which define *what* to do. For example, jobs that compile or test code.
> - Stages, which define *when* to run the jobs. For example, stages that run tests after stages that compile the code.
>
> Jobs are executed by [runners](https://docs.gitlab.com/ee/ci/runners/README.html). Multiple jobs in the same stage are executed in parallel, if there are enough concurrent runners.
>
> If *all* jobs in a stage succeed, the pipeline moves on to the next stage. If *any* job in a stage fails, the next stage is not (usually) executed and the pipeline ends early.
>
> A typical pipeline might consist of four stages, executed in the following order:
>
> - A `build` stage, with a job called `compile`.
> - A `test` stage, with two jobs called `test1` and `test2`.
> - A `staging` stage, with a job called `deploy-to-stage`.
> - A `production` stage, with a job called `deploy-to-prod`.

## Pipeline Architectures

Pipelines constitute the fundamental building blocks of CI/CD. There are three main ways to structure such a pipelines, as briefly elaborated&mdash;along with examples&mdash;in the [GitLab pipeline architecture][gl-pipearch] doc.

> - [Basic](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html#basic-pipelines): Good for straightforward projects where all the configuration is in one easy to find place.
> - [Directed Acyclic Graph](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html#directed-acyclic-graph-pipelines): Good for large, complex projects that need efficient execution.
> - [Child/Parent Pipelines](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html#child--parent-pipelines): Good for monorepos and projects with lots of independently defined components.

# GitLab CI tutorial

The GitLab docs provide [a guide to creating your own CI pipeline][gl-ciguide] which uses a static site for demonstration purposes. We will use the Energy4Life dash application mentioned in another note for this tutorial for hosting ka site. Feel free to follow along with the [Jekyll](https://jekyllrb.com/) Static Site Generator (SSG) from the original guide.

In case the *Pages* menu does not appear as indicated in the tutorial, the issue might be that you [are trying to create them for a group](https://forum.gitlab.com/t/why-is-pages-not-appearing-in-settings/16208/6). In which case it seems you must create a project called `websites.gitlab.io` and build it there.

**NOTE:** Neither the tutorial nor using our E4L code worked for me. The said *Pages* menu did not appear (even after trying it on a personal repository). Additionally our E4L code caused the pipeline to fail since *hosting a site*&mdash;compared to creating a static site like the tutorial demonstrates&mdash;is not a task that can *complete*, thus causing GitLab to terminate the job because it exceeded the 1 hour time limit.

# Parallels to aspects of DevOps and MS

Here we will look at what parts of the GitLab tools are related to DevOps and MS aspects&mdash;which were mentioned in the [Research Question][rqdoc] document.

[rqdoc]: MS-ResearchQuestions

## Automation

As pointed out in the [DevOps section](MS-ResearchQuestions#what-is-the-role-of-ms-in-devops?) of the [research document][rqdoc], automation plays a big role in DevOps and MS. In GitLab we have the ability to create so-called [*pipelines*](#what-are-pipelines?) which handle certain tasks automatically. A pipeline essentially consists of a collection of jobs which are executed at specified stages of the pipeline. Within a job we have&mdash;among many others&mdash;the ability to specify what commands should be run.

Having these commands written out explicitly in this manner first of all allows us to simply run the job&mdash;which in turn will execute the provided instructions&mdash;and have our application be automatically built, tested, deployed or complete whatever task our job is aimed at. As a matter of fact the pipeline, along with its jobs, is run after every push to the repository. Hence we only merely need to change the code and GitLab will take the rest of the work off our shoulders.

## Developer and Operator Collaboration

Another point that was made in the [research document][rqdoc] is that these automation scripts serve as a common reference for every member of a team. And indeed, viewing the GitLab pipeline specification is not restricted solely to a subset of the team. Everyone can see it and glimpse at how the application is built and what stages need to be completed before it gets the green light into production.

In case the pipeline needs a modification, either due to issues or other reasons, it is not simply the role of the operators to take care of handling and solving the situation. If for example the developers need something to change in the pipeline, they can consult directly and in a precise manner with the operators on what would be the best course of action, or maybe they could even implement the modifications themselves directly.

## Shared responsibilities

This point also directly leads us to the briefly mentioned shared responsibilities in the [research document][rqdoc]. Each member of the team is quite closely involved with creating the pipeline. For instance the developers should specify how the the application is to be built; testers should take care of how tests are to be executed; operators handle everything with regards to deploying the product; and so on

Since the whole pipeline is defined within a single configuration file on GitLab, every member of the team can see the role and work of the others and hopefully becomes more inclined to simplify the whole process. In contrast to a monolithic approach where the developer's product is simply handed over to the operators and not looked after anymore by its creators, here everyone is fully engaged with every step of the process. If for instance a developer causes the pipeline to fail, for whatever reason, the whole process will be on halt until solved&mdash;because the code cannot be deployed if previous stages like building or testing have failed.

# GitLab Container Registry

> With the [GitLab Container Registry][gl-registry], every project can have its own space to store images. [More information](https://minsky.uni.lu/gitlab/help/user/packages/container_registry/index). Expiration policies help manage the storage space used by the Container Registry.

More general information about Registry can be found in the [docker docs][d-registry]. For information on GitLab's Registry, check out the [gitlab docs][gl-registry].

To get an idea of how the registry is actually used, please refer to [the configuration and example sections](https://docs.gitlab.com/ee/user/packages/container_registry/#configure-your-gitlab-ciyml-file) of the documentation.

We have the ability to use [docker to build docker images][gl-docker] in GitLab.