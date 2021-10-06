---
layout: post
title: "Monitoring Cloud Applications"
subtitle: "Lecture 9 - Monitoring Cloud Applications (Deadline 2021/10/06 23:55)"
date: 2021-10-06 11:03:22 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

I am going to implement logging (Serilog) into our web application that was made during [Lecture 6](https://orhan92.github.io/jekyll/update/2021/09/24/webapp-in-the-cloud.html). But in short, I am going to implement logging into our Music Web Application.

## The logging Code

I will start off by showing you screenshots of the logging code implemented into the code followed by how you can retrieve important information from the logs posted into Azure Application Insights. In order to actually log into Application Insights I had to head over to the Azure Portal -> My Music Application -> Application Insights -> activate Application Insights. So this was basically the first step I had to do in order to retrieve a Instrumentation Key which we will use to connect our application with the Instrumentation Key. This will let us store logs into the Azure Portal.

After I retrieved the Instrumentation Key, it was time to implement some code into the web application.

![Appsettings](/images/json-instrumentation-key.png){:class="img-fluid"}

Next step was to use Dependency Injection inside our `Startup.cs` class.

![Startup.cs](/images/startup.png){:class="img-fluid"}

I also wanted to make sure to see that our Serilog logging would work inside our console when debugging the application. So had to add some code into our `Main`-method inside `Program.cs`.

![Main.cs](/images/main-logging.png){:class="img-fluid"}

This step will show our Serilog output inside the console as much as it will push the log events into our Application Insights in Azure Portal. So at this point we are almost done with implementing logging into our application. We just have to create some logs inside our application aswell.

![Create Log](/images/create-logs.png){:class="img-fluid"}

Now that we have everything set it is time to actually test the code. Lets start off by looking at our Console Window when we run this application.

![Console Log](/images/console-log.png){:class="img-fluid"}

You can see that we can follow everything that happens within the application and it is quite easy to understand and read what is happening. Since everything is running smoothly we can see `[INF]` which means "Information". If we would have a crash we would probably see `[ERR]` or `[WAR]`. Anyways, our Console is now printing out every step that occures during runtime. I have also drawn a Red Arrow in order to show you that our log created within the `Create`-method is printed into the console.

Great! Our logging works inside our Console. Lets publish our application to the Azure Clouds. But how does it look inside the Application Insights (Azure Portal)? Lets have a look.

![Azure Log](/images/azure-log.jpg){:class="img-fluid"}

We have to Navigate into our Azure Portal -> Web Application -> Application Insights -> Logs. Now that we are inside our `Logs` directory, it is time to have a look at the log traces created when we used the application. The Image above shows us an illustration, together with a `Kusto`-query. We retrieve all traces created within a given timespan (This could of course be changed in order to view different log traces).

## Benefits with logs

I will take a standpoint from our examples above. So what is so good with logs? Well, First of all we can always trace and track every action that has been made inside our application and correct errors/warnings if needed. Second, it is very good to monitor our application in order to keep our application smooth and fast. We can for example easily notice if the responsetime is slow or if our API calls are malfunctioning. So, lets say we have a webshop with a checkout / payment system implemented. Why is logs so good/important? Well, you might have figured some good points out. One of them would be to track where a transaction have gone wrong, or where the system failed to operate. We can then dive into our code and correct the errors.
Another example would be with customer support. Lets say a customer encountered a problem and decides to contact us. We can then, based on when the customer interacted with our website, retrieve the logs for this customer and then identify where our application malfunctioned.
These are all just a few quick examples, but I bet you understand the power of loggings!

### References

[Serilog](https://serilog.net/)\
[Microsft Docs - Application Insights](https://docs.microsoft.com/en-us/learn/modules/instrument-web-app-code-with-application-insights/2-install-application-insights-sdk)\
[Structured Logging](https://codewithmukesh.com/blog/serilog-in-aspnet-core-3-1/)
