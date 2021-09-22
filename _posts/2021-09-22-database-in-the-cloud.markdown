---
layout: post
title: "Database in The Cloud"
subtitle: "Lecture 5 - CosmosDb & Azure Functions (Deadline 2021/09/22 23:55)"
date: 2021-09-22 10:33:12 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

Today we are going to create Azure functions (POST and GET) and a CosmosDB. Main purpose here is that our functions are going to send requests to our database. We should be able to POST and GET information to/from the database. This blog post will describe:

- My application and how it works
- Description of the Source Code
- Description of the CosmosDb
- How did we deploy the Azure functions?
- Database scheme changes (**ÄNDRA DENNA**)
- Estimated cost to run this application (1. Almost no users, 2. Alot of users)

#### Resources for this Project

[My GitHub Repo](https://github.com/Orhan92/CosmosFunction)
[Create Azure Function with VSC](https://docs.microsoft.com/sv-se/azure/azure-functions/create-first-function-vs-code-csharp)
[Create CosmosDb and bind Functions](https://docs.microsoft.com/sv-se/azure/azure-functions/functions-add-output-binding-cosmos-db-vs-code?pivots=programming-language-csharp)

## How does the application work?

This application (See [My GitHub Repo](https://github.com/Orhan92/CosmosFunction)) have two different CRUD-operations: 1. [Post Function](https://github.com/Orhan92/CosmosFunction/blob/main/CosmosFunctionPost.cs) and 2. [Get Function](https://github.com/Orhan92/CosmosFunction/blob/main/CosmosFunctionGet.cs).

#### Post Function

![Post Function](/images/function-post.png){:class="img-fluid"}

#### Get Function

![Get Function](/images/function-get.png){:class="img-fluid"}
