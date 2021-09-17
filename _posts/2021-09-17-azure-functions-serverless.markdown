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

<pre class="csharp" style="font-family:monospace;"><span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.IO</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Threading.Tasks</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.AspNetCore.Mvc</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Azure.WebJobs</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Azure.WebJobs.Extensions.Http</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.AspNetCore.Http</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Extensions.Logging</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Newtonsoft.Json</span><span style="color: #008000;">;</span>
&nbsp;
<span style="color: #0600FF; font-weight: bold;">namespace</span> LocalFunctionProj
<span style="color: #008000;">&#123;</span>
    <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #6666cc; font-weight: bold;">class</span> HttpExample
    <span style="color: #008000;">&#123;</span>
        <span style="color: #008000;">&#91;</span>FunctionName<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;HttpExample&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span>
        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task<span style="color: #008000;">&lt;</span>IActionResult<span style="color: #008000;">&gt;</span> Run<span style="color: #008000;">&#40;</span>
            <span style="color: #008000;">&#91;</span>HttpTrigger<span style="color: #008000;">&#40;</span>AuthorizationLevel<span style="color: #008000;">.</span><span style="color: #0000FF;">Anonymous</span>, <span style="color: #666666;">&quot;get&quot;</span>, <span style="color: #666666;">&quot;post&quot;</span>, Route <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">null</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span> HttpRequest req,
            ILogger log<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            log<span style="color: #008000;">.</span><span style="color: #0000FF;">LogInformation</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;C# HTTP trigger function processed a request.&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
            <span style="color: #6666cc; font-weight: bold;">string</span> name <span style="color: #008000;">=</span> req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;name&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">;</span>
&nbsp;
            <span style="color: #6666cc; font-weight: bold;">string</span> requestBody <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">await</span> <span style="color: #008000;">new</span> StreamReader<span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Body</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0000FF;">ReadToEndAsync</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #6666cc; font-weight: bold;">dynamic</span> data <span style="color: #008000;">=</span> JsonConvert<span style="color: #008000;">.</span><span style="color: #0000FF;">DeserializeObject</span><span style="color: #008000;">&#40;</span>requestBody<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            name <span style="color: #008000;">=</span> name <span style="color: #008000;">??</span> data<span style="color: #008000;">?.</span><span style="color: #0000FF;">name</span><span style="color: #008000;">;</span>
&nbsp;
            <span style="color: #0600FF; font-weight: bold;">return</span> name <span style="color: #008000;">!=</span> <span style="color: #0600FF; font-weight: bold;">null</span>
                <span style="color: #008000;">?</span> <span style="color: #008000;">&#40;</span>ActionResult<span style="color: #008000;">&#41;</span><span style="color: #008000;">new</span> OkObjectResult<span style="color: #008000;">&#40;</span>$<span style="color: #666666;">&quot;Hello, {name}&quot;</span><span style="color: #008000;">&#41;</span>
                <span style="color: #008000;">:</span> <span style="color: #008000;">new</span> BadRequestObjectResult<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;Please pass a name on the query string or in the request body&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span>
    <span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span></pre>

At this point all the function did was to take a name input and printed it out to the user. I had to modify this code to act like an addition calculator. I also added a try and catch clausule to make sure we had some error handling in the code. You can see the source code in my repository by clicking this link: [CalculatorFunction with Try and Catch](https://github.com/Orhan92/AzureFunctionCalculator/blob/main/CalculatorFunction.cs).

After I modified the code, the function looked like this:

<pre class="csharp" style="font-family:monospace;"> <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #6666cc; font-weight: bold;">class</span> CalculatorFunction
    <span style="color: #008000;">&#123;</span>
        <span style="color: #008000;">&#91;</span>FunctionName<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;CalculatorFunction&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span>
        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task<span style="color: #008000;">&lt;</span>IActionResult<span style="color: #008000;">&gt;</span> Run<span style="color: #008000;">&#40;</span>
            <span style="color: #008000;">&#91;</span>HttpTrigger<span style="color: #008000;">&#40;</span>AuthorizationLevel<span style="color: #008000;">.</span><span style="color: #0000FF;">Function</span>, <span style="color: #666666;">&quot;get&quot;</span>, <span style="color: #666666;">&quot;post&quot;</span>, Route <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">null</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#93;</span> HttpRequest req,
            ILogger log<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            log<span style="color: #008000;">.</span><span style="color: #0000FF;">LogInformation</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;C# HTTP trigger function processed a request.&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
            <span style="color: #0600FF; font-weight: bold;">try</span> 
            <span style="color: #008000;">&#123;</span>
                <span style="color: #6666cc; font-weight: bold;">string</span> name <span style="color: #008000;">=</span> req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;name&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">;</span>
                <span style="color: #6666cc; font-weight: bold;">string</span> requestBody <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">await</span> <span style="color: #008000;">new</span> StreamReader<span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Body</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0000FF;">ReadToEndAsync</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
                <span style="color: #6666cc; font-weight: bold;">dynamic</span> data <span style="color: #008000;">=</span> JsonConvert<span style="color: #008000;">.</span><span style="color: #0000FF;">DeserializeObject</span><span style="color: #008000;">&#40;</span>requestBody<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
                name <span style="color: #008000;">=</span> name <span style="color: #008000;">??</span> data<span style="color: #008000;">?.</span><span style="color: #0000FF;">name</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #6666cc; font-weight: bold;">double</span> firstValue <span style="color: #008000;">=</span> <span style="color: #FF0000;">0</span>, secondValue <span style="color: #008000;">=</span> <span style="color: #FF0000;">0</span><span style="color: #008000;">;</span>
&nbsp;
                firstValue <span style="color: #008000;">=</span> Convert<span style="color: #008000;">.</span><span style="color: #0000FF;">ToDouble</span><span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;firstValue&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
                secondValue <span style="color: #008000;">=</span> Convert<span style="color: #008000;">.</span><span style="color: #0000FF;">ToDouble</span><span style="color: #008000;">&#40;</span>req<span style="color: #008000;">.</span><span style="color: #0000FF;">Query</span><span style="color: #008000;">&#91;</span><span style="color: #666666;">&quot;secondValue&quot;</span><span style="color: #008000;">&#93;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #0600FF; font-weight: bold;">var</span> sum <span style="color: #008000;">=</span> firstValue <span style="color: #008000;">+</span> secondValue<span style="color: #008000;">;</span>
                <span style="color: #6666cc; font-weight: bold;">string</span> responseMessage <span style="color: #008000;">=</span> $<span style="color: #666666;">&quot;Name: {name}<span style="color: #008080; font-weight: bold;">\n</span>First Value: {firstValue}<span style="color: #008080; font-weight: bold;">\n</span>Second Value: {secondValue}<span style="color: #008080; font-weight: bold;">\n</span>Total sum: {firstValue} + {secondValue} = {sum}&quot;</span><span style="color: #008000;">;</span>
&nbsp;
                <span style="color: #0600FF; font-weight: bold;">return</span> <span style="color: #008000;">new</span> OkObjectResult<span style="color: #008000;">&#40;</span>responseMessage<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #008000;">&#125;</span>
&nbsp;
            <span style="color: #0600FF; font-weight: bold;">catch</span> 
            <span style="color: #008000;">&#123;</span>
                <span style="color: #0600FF; font-weight: bold;">return</span> <span style="color: #008000;">new</span> BadRequestObjectResult<span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;Invalid calculations. Try again!&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #008000;">&#125;</span>
        <span style="color: #008000;">&#125;</span>
    <span style="color: #008000;">&#125;</span></pre>

So the code above now works as a calculator function which takes your `name`, `firstValue` and `secondValue` as a input. Then based on those first and second value the output will show a sum (result).

At this point the Bronze part is done and it is time to move on to the **Silver** exercise.
