---
layout: post
title: "Azure Functions - Serverless"
subtitle: "Lecture 4 - Azure Functions (Deadline 2021/09/17 23:55)"
date: 2021-09-12 13:37:12 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

During this lecture we have been activating our Azure for students account where Azure will sponsor students with 100$ that is available for 12 months. This is order to learn how Azure works and to play around. After the registration we downloaded and installed [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli) in order to be able to run Azure commands with a CLI such as `az login`. But I also wanted to be able to run azure function commands inside my CLI, so I had to download and install [Azure Function Core Tools](https://docs.microsoft.com/sv-se/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cportal%2Cbash%2Ckeda#v2). This tool would allow me to run commands such as `func start` and run the azure function locally.

After I installed and setup the prerequisites above it was time to tackle some exercises. The exercises was all about building a Azure Functions application that was supposed to act like a mini calculator that took two integer or double inputs and added them together to show the sum of these. In other words we were going to create a simple Additional Calculator with Azure Functions. We were going to use **HttpTrigger**-template.
There was 3 different exercise difficulties:

- Bronze
- Silver
- Gold

**Bronze:** Build the function with Azure Web interface (inside Azure Dashboard) OR directly insie Visual Studio or Visual Studio Code.

**Silver:** Build the function (not with Azure Web interface) and then create a GitHub repository for your function. The challenge here is to deploy your Azure Function application directly from GitHub into Azure with CLI.

**Gold:** Deploy your Azure Function application from GitHub Actions, in other words create a CI-pipeline with GitHub Actions that deploys your function.

In this blog post I will go through **Bronze**, **Silver** and **Gold** steps and how I did to solve these exercises. Before we do that, I will make a short description och the term 'Serverless' and Function as a Service (FaaS).

## What is Serverless and FaaS?

Serverless is a cloud computing model where applications can run without actually managing any servers. So what does this mean? It doesn't mean that there is no servers running. What it actually means is that the server is a cloud-based model which the providers will manage and handle instead of letting the customers or developers do that. This way the developers can focus on building their applications and focus on their code rather than managing the servers. Everything that has with the server to do is handeled by the provider such as Azure, AWS or Google Cloud Platform. These providers will manage and handle everything that has with the servers to do and the developers can concentrate on logic for their applications. The customer is paying for the resources being used on their applications. Once serverless is deployed, the applications it powers start responding to demands quickly and scale up or down automatically as required.

FaaS (Function as a Service) is a serverless way to execute pieces of code. With Faas developers can focus on writing and updating code which then can be executed in response to an event or request. This could be for example if a user clicks on an element inside of an webapplication. Then FaaS can be called upon that element and trigger the function. This is also a cost-efficient way to implement microservices for your web application. By using Serverless code like FaaS, developers can focus on writing code while the providers (Azure, AWS or Google Cloud) takes care of the server and backend services for the server.

## Bronze Exercise

The first step was to actually create the function. I chose to do so by using my CLI and executed commands to actually create my project. For further details see [Create HttpTrigger Azure function with C# with CMD](https://docs.microsoft.com/sv-se/azure/azure-functions/create-first-function-cli-csharp?tabs=in-process%2Cazure-cli). After all steps was executed, I created a folder which contained my Azure HttpTrigger function which looked like this:

```cs
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace LocalFunctionProj
{
    public static class HttpExample
    {
        [FunctionName("HttpExample")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            dynamic data = JsonConvert.DeserializeObject(requestBody);
            name = name ?? data?.name;

            return name != null
                ? (ActionResult)new OkObjectResult($"Hello, {name}")
                : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
        }
    }
}
```

At this point all the function did was to take a name input and printed it out to the user. I had to modify this code to act like an addition calculator. I also added a try and catch clausule to make sure we had some error handling in the code.
