---
layout: post
title: "Continuous Integration"
subtitle: "Lecture 2 - Continuous Integration Pipelines (Deadline 2021/09/09 23:55)"
date: 2021-09-08 15:49:22 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

This post will be about CI (Continuous Integration) pipelines. I will go through fundamental basics of CI and what it is (atleast I will try to explain it as good as I can). Furthermore I will try to implement a CI Pipeline with GitHub Actions into an already existing repository/project. I will do this by 'forking' an GitHub repository/project. My goal is to make sure that the CI Pipeline activates on every commit to our fork on all branches. When I have succeeded with that I will try to explain the necessary steps that I took and how the process looked like in this blog post. I will also describe my GitHub action YAML file.

## What is a CI Pipeline?

A CI (Continuous Integration) can be seen as a "approving" or "test" mechanism that safely builds the code and tests that have been written and checks them before actually deploying the changes into the main branch. This step is called a check-in. Once the code build and tests have been approved by the CI pipeline, we can then safely deploy the new version of the application, which is called CD (Continuous Deployment). With CI, every change in the code will trigger the pipeline and provide feedback to the developer or developers who made the change. If it passes the CI, then we can safely move to CD and deploy our changes. CI is automated checks before releasing new code that developers has committed. But CI doesn't come from nowhere, someone has to build the infrastructure of the CI Pipeline. I will go through how I built mine in a section below.

### Why is it important to use CI Pipelines?

The importance of using CI Pipelines is maybe to some very clear and to others not so clear. But as I mentioned in the section above, there are alot of benefits with CI Pipelines. We don't have to review thousands and thousands row of code in order to find where an error or bug in our application occured. With CI Pipelines, developers will push snippet changes of code and those will be reviewed by the automated pipeline before actually being merged into a branch of importance and eventually being deployed (CD).

- Automated Pipelines can detect errors before we push the code for deployment
- Detect bugs or failed tests
- Provide feedback to the developers on where the code is malfunctioning
- Enable continuous and fast delivery of code
- You can test if the application build on other virtual machines, you can specify version of platform or even operating systems to see if the code passes or not.
- Makes it easier to work Agile.

<!-- https://semaphoreci.com/continuous-integration
https://semaphoreci.com/blog/cicd-pipeline
https://explainagile.com/agile/xp-extreme-programming/practices/continuous-integration/ -->

## How did I create my CI Pipeline?

Okay, first of all I have forked an older project called Spacepark v1 (spacepark-spaceinvaders). The original repository can be found here: [Spacepark v1](https://github.com/PGBSNH20/spacepark-spaceinvaders).
By forking a repository means that I copy that repository and create an exact copy of it in my own repository (see the link): [Forked Spacepark v1](https://github.com/Orhan92/spacepark-spaceinvaders).

Next step in the process was to create a YAML file inside my new forked repository. But before I show you what that looks like, make sure that whenever you create that YAML file, create it in a new branch and NOT inside of main as we will test the CI Pipeline when we push from this newly created branch into the main branch. It should look something like this:
![Filepath for YAML File](/images/yamlworkdir.png){:width="650px"}

As you can see in the image above, I started by creating a '.github' folder in root of the newly created branch (CI-test-&-build) followed by another folder called 'workflows', and inside the 'workflows' folder i created my YAML file called 'CI-test-&-build' because that is exactly what my pipeline will do!

Okay great.. so now we have to actually fill the content inside of our newly created YAML file so that we can configure a pipeline and what jobs should be done inside of it.

If you follow this link: [YAML File](https://github.com/Orhan92/spacepark-spaceinvaders/blob/CI-test-%26-build/.github/workflows/CI-test-&-build.yml), you will get straight to my configured YAML file to see what it looks like. Or if you prefer, have a look at the image below.
![Filepath for YAML File](/images/yamlfile.png){:width="650px"}
