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

At this point the Bronze part is done and it is time to move on to the **Silver** exercise as this function only works locally at this point because we have not deployed it into Azure Portal. That is what we will do in the **Silver** step. But so far we can run our function locally by being inside the working directory inside our CMD. In my case I execute this command: `cd source/repos/LocalFunctionProj`. Once inside the working directory we can execute `func start` command in order to run our function locally. If we then navigate to the localhost (which will be given to you inside your CMD), we can then see the behaviour of our function and add values to the function. See the image illustration below where the highlighted part is where I add the value inputs.

![Local function](/images/localfunc.png){:class="img-fluid"}

## Silver Exercise

I started with pushing my created project from the **Bronze** step to GitHub using my Command Line. For further details see [Adding an existing project to GitHub using the Command Line](https://docs.github.com/en/github/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line).

Now that we have created a GitHub repository to our project it is time to deploy this function into Azure from GitHub. The documentation I followed is from this link: [Deploy Azure Function app from GriHub](https://docs.microsoft.com/bs-cyrl-ba/azure//azure-functions/scripts/functions-cli-create-function-app-github-continuous).

This is the code that was used to create and deploy our code from GitHub to Azure (These where executed from CMD):

<pre class="yaml" style="font-family:monospace;"><span style="color: blue;"># Enable authenticated git deployment in your subscription from a private repo.</span>
az functionapp deployment source update-token \
  --git-token $token
&nbsp;
<span style="color: blue;"># Create a resource group.</span>
az group create \
  --name myResourceGroup \
  --location $region
&nbsp;
<span style="color: blue;"># Create an Azure storage account in the resource group.</span>
az storage account create \
  --name $storageName \
  --location $region \
  --resource-group myResourceGroup \
  --sku Standard_LRS
&nbsp;
<span style="color: blue;"># Create a function app with source files deployed from the specified GitHub repo.</span>
az functionapp create \
  --name $functionAppName \
  --storage-account $storageName \
  --consumption-plan-location $region \
  --resource-group myResourceGroup \
  --deployment-source-url $gitrepo \
  --deployment-source-branch master \
  --functions-version 2</pre>

So the first step was to navigate to **GitHub --> Account --> Settings --> Developer settings --> Personal Access tokens --> Generate new token**. See the [Step-by-Step instructions](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) for how to setupd the token. Make sure to copy the token and follow the instructions from the code block above.

What I did was to follow every instruction in the code block shown above. When that was done I could login to my Azure portal and see that my resources have been created. It should look like this based on your configuration:

![Azure portal](/images/azureportal.png){:class="img-fluid"}

Great, we have now created our resource group inside Azure portal from our GitHub repository. But there was one last thing to do before actually being able to Test & Run the function inside Azure portal because our function have been deployed as Read-Only. To go around that and make it possible to Test our function inside Azure portal I had to run this command: `func azure functionapp publish AdditionCalculator --force --nozip`. Once this command was run I could actually start testing my function inside the portal and it was no longer Read-Only mode. I had access to the function now. I had to go through this link to figure out how to work around this problem: [Disable Read Only](https://stackoverflow.com/questions/53630773/how-to-disable-read-only-mode-in-azure-function-app).

Now that we have deployed our function app into Azure Portal it is time to move on to the **Gold** Exercise and create a pipeline to deploy our function into Azure.

## Gold Exercise

So, what I did to make this GitHub Actions Pipeline work was to read through [Azure Functions Action](https://github.com/marketplace/actions/azure-functions-action) carefully. After I had gone through every step, My pipeline was now working properly with Repository Secrets and my YAML file. See my [YAML-File](https://github.com/Orhan92/AzureFunctionCalculator/blob/main/.github/workflows/azure-deployment.yml) to see how to pipeline works. If you look at the 'Azure Functions Action' link above, then you will see exactly step-by-step instructions of how I did to deploy my application through a pipeline with GitHub Actions. See the image below for an overview of my workflow action:

![Azure pipeline](/images/azurepipeline.png){:class="img-fluid"}

So, if the Action passes, then our workflow will deploy this to Azure and our changes will be applied to our function. For instance if I would remove the calculator function and go back to the original state of the function (only name input) and then commit this change. We would first go through the Pipeline and then once the workflow jobs have passed it will be deployed to Azure and our function will no longer contain a calculator. This is how the pipeline works.

## Function Tests

There are many different ways to test this function. One way was to test it locally as I showed before in the **Bronze** section. But it is also possible to test this function through Azure Portal. See the image below for illustration for inputs:

![Input Azure Portal](/images/azureportalinput.png){:class="img-fluid"}

Output:

![Output Azure Portal](/images/azureportaloutput.png){:class="img-fluid"}

Second way of testing our function is to test it with the public URL which you can test from your computer if you want to. See the image illustration below where the highlighted part is the input values:

![Public URL](/images/publicurltest.png){:class="img-fluid"}

Lastly we can also test this function through Postman Services which I did. So the first thing we have to to inside our postman is to add this URL to the Postman so that we know where to look for the function: `https://additioncalculator.azurewebsites.net/api/CalculatorFunction?code=tBhXSSny7Vv/NjBCgj1watiBiBkRxQEbjcfwGr/Il/dEgh0ePFb0EQ==`. Once inside our Postman Agent we can test GET and POST method and give it values to try the function out.

Postman GET illustration below:

![Postman GET](/images/postmanget.png){:class="img-fluid"}

Postman POST illustration below:

![Postman POST](/images/postmanpost.png){:class="img-fluid"}

Postman POST Illustration with errorhandling, the highlighted area is where I show invalid input and the output from the API/Function:

![Postman Invalid POST](/images/postmaninvalid.png){:class="img-fluid"}

As you can see our Try and Catch clausule which can be found in my [Function file](https://github.com/Orhan92/AzureFunctionCalculator/blob/main/CalculatorFunction.cs) handeled the error and gave us a BadRequest in return.

## Security Issues

I haven't really considered the security threats available here in this function application because I found no need for it here. But of course if this were to be a bigger application of matter or distributed to clients, then security meassurements have to be considered. But one thing that can be considered in this function is that there is no authentications needed to use the API/function. Therefore it is open to anyone for usage. One thing that could be added into this function is Authentication through API Keys to restrict this function for public use.

### References

##### Prerequisities

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)

[Azure Function tools](https://docs.microsoft.com/sv-se/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cportal%2Cbash%2Ckeda#v2)

##### Serverless and FaaS description

[About Serverless](https://geekflare.com/know-about-serverless/)

[Why Serverless?](https://hackernoon.com/why-serverless-why-now-f09ce43c4767)

[Function as a Service](https://www.cloudflare.com/learning/serverless/glossary/function-as-a-service-faas/)

[Learn about FaaS](https://www.ibm.com/se-en/cloud/learn/faas)

##### Create a function project with CLI

[Create Azure function with CLI](https://docs.microsoft.com/sv-se/azure/azure-functions/create-first-function-cli-csharp?tabs=in-process%2Cazure-cli)

##### GitHub Deployment

[Deployment with CLI](https://docs.microsoft.com/bs-cyrl-ba/azure//azure-functions/scripts/functions-cli-create-function-app-github-continuous)

[Generate Access Token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)

##### Troubleshooting

[Disable Read Only mode](https://stackoverflow.com/questions/53630773/how-to-disable-read-only-mode-in-azure-function-app)
