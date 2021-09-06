---
layout: post
title: "First Blog Post"
subtitle: "Lecture 1 - Blog post (Deadline 2021/09/07 23:55)"
date: 2021-09-06 19:10:45 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

Today we have been learning about different cloud solutions such as IaaS (Infrastructure as a Service) PaaS (Platform as a Service) and SaaS (Software as a Service). We have been going through basics of networking and some recap from our last course before the summer break. That recap included: .NET Core, Razor Pages, Dependency Injections, Docker, API, Security and more. I am not going to go through everything that have been recapped during todays lecture. I just wanted to give you a brief overview of what we have been doing during our lecture today.

The most interesting part in todays lecture was going through and touching the tip of the fundamentals and basics of cloud solutions. In the section below I will try to do my best to explain my perception of what 'The Cloud' is.

The Cloud offers different kind of Cloud Services. There are types as 'Public Clouds', 'Private Clouds' and 'Hybrid Clouds'. Each one of these have different characteristics.

**Public Clouds** - Are offered through the internet and is available for anyone who wants to buy/subscribe to it. Servers and storages are owned by extern providers and the end user only pays for the resources being used.

**Private Clouds** - Clouds being used within a company or organisation where some users can be restricted from accessing data. This type of cloud has to be bought and the company is responsible for startup and all kind of maintenance regarding the cloud. On the bright side, the company has full control over resources and security regarding the cloud rather than having a extern provider managing and maintaining the cloud.

**Hybrid Clouds** - This type of cloud is a combination between **public** and **private** clouds. It is more flexible. A really good benefit with Hybrid Clouds is that work can be done more agile.

_I am still in the very beginning of my learning curve regarding clouds. If there is anything that is completely bananas in the explanation above, please let me know!_

## Benefits with Cloud Solutions

- Good availability and user experiences.
- Scaling. With cloud solutions you can easily scale your program or application. You can always add/remove resources, instances, storage and so on.
- Flexibility, you can easily and quickly configure your resources to match your applications new demands. That is why Clouds are really good for agile work.
- Geo-distribution, makes it easy and available to distribute your application throughout the world by setting up a server in that specifik geo-location.
- Data backup, version controls and replication in case your application would break due to any given reason.

Another good benefit with Cloud Solutions is OpEx. OpEx means that the organisation using the cloud only pays for the resources being used. Which is really cost efficient. You dont have to pay any starting fees, no unnessescary resource purchases that might not even be used to the fullest and you can also remove/end resources which is not being used to reduce your cost.

## We cannot forget the Disadvantages..

Meanwhile there is alot of benefits with Cloud Services, we cannot blindly look away at the disadvantages. There are a few disadvantages aswell:

- It is not very cheap. Costs alot as you expand your application or program.
- Data losses or thefts.
- Security breaches

There certainly are much more to the list but these are the ones that I can come up with for now.. Please let me know if there is anything you would like to add to the list.

## Most commonly used Cloud Services

As I mentioned in the introduction there are three main type of services. Those are **IaaS**: Infrastructure as a Service. This type of service is like hiring a virtual machine. In this type of service, the provider will handle the infrastructure of the cloud meanwhile you as a user can focus on maintenance of what you've been establishing. The user doesn't have to understand the technique behind the infrastructure, that is for the provider to handle meanwhile they have to guarantee safety and availability.

Then we have **PaaS**: Platform as a Service. Same benefits as the previous service (IaaS), but now the user has access to more advanced tools that they can use. With PaaS the user can focus more on programming and development rather than focusing on the platform or the infrastructure. This also provides better agility to work agile because the platform can now be reached through the internet. And it is much easier to make host globally. But there are always some platform restrictions. Make sure you always check which kind of restrictions that are present on the specifik platform. Then you have to decide if you can run your program or applikation smoothly throughout that platform without encountering problems or restrictions.

Lastly we have **SaaS**: Software as a Service. The name reveals itself. You might have heard of Office365. Exactly, that is a SaaS. Software is being distributed centrally and this type of cloud service often comes with a prenumeration fee. It is easy to access this type of software, it is easy to give access to your employees and you can access the software wherever you are.

## Comparison

**IaaS** - The most flexible service
**PaaS** - Makes it possible to focus on development rather than the platform itself. Buys alot of time.
**SaaS** - Users pay for the software, ususally with a subscription of monthly or yearly fees. And it is accessible everywhere.

_I have to point it out once again. I am still in the very beginning of my learning curve regarding clouds. If there is anything that is completely bananas in the explanation above, please let me know!_

## Well-known Providers | Pricing Calculation (Cloud Service)

In the section below, I have made price calculations to find out pricing ranges within the Giant Cloud providers compared to smaller ones. For this example we have made up a scenario where we need a server that should run a simple website with a simple database, alternatively everything installed on the same server. Requirements: 2 vCPU, 8GB RAM, 10GB Storage on a LINUX Server in Europe. The server should be up and running 24/7.

Down below I will start with listing the Giants followed by the smaller providers.

---

### Amazon Pricing Calculation (Cloud service)

![Aws1](/orhan92/images/AWS1.png){:width="800px"}

![Aws2](/orhan92/images/AWS2.png){:width="800px"}

![Aws3](/orhan92/images/AWS3.png){:width="800px"}

---

### Google Pricing Calculation (Cloud Service)

![Google](/orhan92/images/Google.png){:width="800px"}

---

### IBM Pricing Calculation (Cloud Service)

![IBM](/orhan92/images/IBM.png){:width="800px"}

---

### Azure Pricing Calculation (Cloud Service)

![Azure](/orhan92/images/Azure.png){:width="800px"}

---

## Smaller providers | Pricing Calculation (Cloud Service)

---

### Hetzner

![Hetzner](/orhan92/images/Hetzner.png){:width="800px"}

---

### Ecompute

![Ecompute](/orhan92/images/Ecompute.png){:width="800px"}

---

### Digital Ocean

![DigitalOcean](/orhan92/images/DigitalOcean.png){:width="800px"}

---

# Conclusion

As we can see, the bigger (giants) providers are far more expensive than the smaller providers. This could have many reasons which I havn't explored yet (which I will surely do!). But one thinkable reason could be the amount of resources or tools that the bigger providers can provide. It could also be a matter of security, processing, accessability, flexibility, speed etc. If I were to run a big organisation then I would probably not have any troubles affording Azure services, Google, IBM or AWS. But as a startup or relatively new company then those costs would probably have a great impact on the economy of the company. My personal approach would be to start with a smaller provider (as you probably can afford it) and then as the company scales you could always consider upgrading or changing provider.

-_Orhan Albayati_
