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

#### References

[Continuous Integration](https://semaphoreci.com/continuous-integration)

[Continuous Integration - Pipelines](https://semaphoreci.com/blog/cicd-pipeline)

[Continuous Integration - Agile](https://explainagile.com/agile/xp-extreme-programming/practices/continuous-integration/)

## How did I create my CI Pipeline?

Okay, first of all I have forked an older project called Spacepark v1 (spacepark-spaceinvaders). The original repository can be found here: [Spacepark v1](https://github.com/PGBSNH20/spacepark-spaceinvaders).
By forking a repository means that I copy that repository and create an exact copy of it in my own repository (see the link): [Forked Spacepark v1](https://github.com/Orhan92/spacepark-spaceinvaders).

Next step in the process was to create a YAML file inside my new forked repository. But before I show you what that looks like, make sure that whenever you create that YAML file, create it in a new branch and NOT inside of main as we will test the CI Pipeline when we merge it from this newly created branch into the main branch. It should look something like this:
![Filepath for YAML File](/images/yamlworkdir.png){:width="650px"}

As you can see in the image above, I started by creating a '.github' folder in root of the newly created branch (CI-test-&-build) followed by another folder called 'workflows', and inside the 'workflows' folder i created my YAML file called 'CI-test-&-build' because that is exactly what my pipeline will do!

Okay great.. so now we have to actually fill the content inside of our newly created YAML file so that we can configure a pipeline and what jobs should be done inside of it.

If you follow this link: [YAML File](https://github.com/Orhan92/spacepark-spaceinvaders/blob/CI-test-%26-build/.github/workflows/CI-test-&-build.yml), you will get straight to my configured YAML file to see what it looks like. Or if you prefer, have a look at the image below.

![Filepath for YAML File](/images/yamlfile.png){:width="650px"}

To start my journey I found get a very good .NET template at [Github](https://docs.github.com/en/actions/guides/building-and-testing-net) that I modified to suit my Spacepark v1 project. I recommend you to look at the link because there is some very good documentation inside of it and might be helpful on your journey aswell. In the beginning I didn't have ny idea of what jobs, steps, uses (also called Actions) and so on meant. Actually I didn't have any clue until I came across this [Github Actions Documentation](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions). Another very good recommendation is to check this [GitHub Workflow Syntax](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#about-yaml-syntax-for-workflows) out. Anyways, below I will go through my [YAML File](https://github.com/Orhan92/spacepark-spaceinvaders/blob/CI-test-%26-build/.github/workflows/CI-test-&-build.yml) step by step with you so that you might get a hint or clue of what each step means.

### Explanation of YAML File

#### Workflows

The workflow is the actual flow of the Pipeline. For example a workflow can be made of one or more jobs to be triggered by an event. The workflow is used to build, test, package, release, review and approve a project or commit on GitHub.

#### Events

An event is the cause that triggers the workflow. In my case, whenever I commit some new code into the repository I will trigger the workflow. The workflow will then start to run based on the instructions I have been giving it inside of my [YAML File](https://github.com/Orhan92/spacepark-spaceinvaders/blob/CI-test-%26-build/.github/workflows/CI-test-&-build.yml).

#### Name

The name as you can see in the picture above is only the name for this particular workflow. In this case: `name: CI Pipeline build & tests`.

#### Runners

These are instructions that listens for available jobs. It runs one job at the time, keeps logs and reports the results. In my case we have runners on different occassions. First on row 3 where we are telling our workflow to work inside the [Source Directory](https://github.com/Orhan92/spacepark-spaceinvaders/tree/CI-test-%26-build/Source) which is also where we have both projects. This way our YAML file knows what to work with as the root folder doesn't contain any project files.

#### jobs

A job is basically what we are telling our workflow to do. For instance, in the YAML file in this case, we can see that the first job is to configure the job to build and run on ubuntu-latest `runs-on: ubuntu-latest`. In this case the job will execute on a virtual machine hosted by GitHub. Followed by the strategy and matrix where we tell the job to look if we can build our code on `dotnet-version: ['5.0.x']`. REMEMBER that all these jobs or steps are made sequentially and if one fails then the job will fail or get canceled (then you have to look inside the logs to see what's wrong).

#### steps

This is where all the steps are grouped together to run where each item is nested and executed sequentially. So in the first step we are using this line of code: `- uses: actions/checkout@v2` where we basically tell the job to retrieve version 2 of the community action. This one is checking out your action from your repository and downloads it into the runner where you can for example test against the code inside your repository. Next step is to setup the IDE inside the job which in this case is .NET Core SDK and .NET version 5.0.x:

```
- name: Setup .NET Core SDK ${{ matrix.dotnet-version }}
uses: actions/setup-dotnet@v1.7.2
with:
dotnet-version: ${{ matrix.dotnet-version }}
```

After the instructions above it is actually time for your workflow to go through what you want it to go through. In this case we will need to install every dependency that the project contains, we will see if the project can build without any failures and lastly we will run the tests within the repository which can be found here: [SpaceTest](https://github.com/Orhan92/spacepark-spaceinvaders/tree/CI-test-%26-build/Source/SpaceTest).

```
- name: Install dependencies
run: dotnet restore
- name: Build
run: dotnet build --configuration Release --no-restore
- name: Test
run: dotnet test --no-restore --verbosity normal
```

As I said before these actions are all executed sequentially which means if the workflow can't build the project, it won't even bother going through the tests and a 'Failure' response will be sent out to the developer(s) who committed the change.

If, everything is fine and we passed the test(s), we can then safely accept the commit and merge it, after approval, into the [Main](https://github.com/Orhan92/spacepark-spaceinvaders/blob/main/.github/workflows/CI-test-%26-build.yml) branch for CD (Continuous Deployment). Thereafter we can delete the branch we created in the beginning safely.

To illustrate the sequential steps that are made by the workflow see the image below:

![Job finished](/images/Job.png){:width="650px"}

In the image you can clearly see that the workflow have sequentially executed all the steps or instructions that we gave inside the [YAML File](https://github.com/Orhan92/spacepark-spaceinvaders/blob/CI-test-%26-build/.github/workflows/CI-test-&-build.yml). And if everything passes, great, you are ready to merge it into the main branch and voila, you have now created your first CI Pipeline (even if it is a basic one that only builds and runs the tests).

### Flaws inside this CI Pipeline

I have excluded parts of the source code tests where those tests are running/checking against a local database, which in this case wont work. For it to work, I would probably have to remake the database structure into SQLite or Dockerize the database so that we can access it through the YAML file. But in this case, our database only exists locally, therefore I had to Exclude some tests which can be seen here: [Tests](https://github.com/Orhan92/spacepark-spaceinvaders/blob/main/Source/SpaceTest/UnitTest1.cs). Look for these lines of code:

```
[Fact(Skip = "Because of local database can't be read in GitHub CI, remove this Skip once another database have been setup like (SQL or through Docker)")]
public void When_Inserting_ParkingData_In_Database_Expect_AnakinSkywalker_ParkingPrice_1h()
```

#### References

[Understanding Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)

[Workflow Syntax](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#about-yaml-syntax-for-workflows)

[Example Templates](https://docs.github.com/en/actions/guides/building-and-testing-net)

[Introduktion to GitHub Actions](https://gabrieltanner.org/blog/an-introduction-to-github-actions)
