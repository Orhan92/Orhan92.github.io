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

[My GitHub Repo](https://github.com/Orhan92/CosmosFunction)\
[Create Azure Function with VSC](https://docs.microsoft.com/sv-se/azure/azure-functions/create-first-function-vs-code-csharp)\
[Create CosmosDb and bind Functions](https://docs.microsoft.com/sv-se/azure/azure-functions/functions-add-output-binding-cosmos-db-vs-code?pivots=programming-language-csharp)

## How does the application work?

This application runs in the cloud with two different functions that are binded to a CosmosDb. We have One GET function and one POST function. Both functions are using HTTP trigger as event listener. We use the POST function to post data into our CosmosDb and then we use the GET function to get data from our CosmosDb. The Post function requires an 'artist' and a 'title' input in order to execute and add data into the database. The Get function requires a 'searchterm' value which will search for an 'artist', based on the 'searchterm' input, inside our CosmosDb. If we get any match, the Get function will retrieve data based on the searchterm value from the database and send the response back to us. Se images below for illustration.

#### Post with Postman

![Post into Database](/images/post-cosmosdb.png){:class="img-fluid"}

#### Get with Postman

![Get into Database](/images/get-cosmosdb.png){:class="img-fluid"}

This application also contains a Pipeline which deploys our functions in Azure portal. This means that whenever we make any changes into our functions, our pipeline will run and if it succeeds, our functions will be deployed to the portal with the new changes applied. So there is no need to manually deploy our changes as our pipeline will do that for us. See this [YAML Pipeline file](https://github.com/Orhan92/CosmosFunction/blob/main/.github/workflows/Deploy%20Azure%20Function.yml) to view pipeline code.

## Description of the Source Code

This application (See [My GitHub Repo](https://github.com/Orhan92/CosmosFunction)) have two different Endpoints and a Model class:

1. [Post Function](https://github.com/Orhan92/CosmosFunction/blob/main/CosmosFunctionPost.cs)
2. [Get Function](https://github.com/Orhan92/CosmosFunction/blob/main/CosmosFunctionGet.cs)
3. [Model class](https://github.com/Orhan92/CosmosFunction/blob/main/SongModel.cs)

#### Post Function

![Post Function](/images/function-post.png){:class="img-fluid"}

#### Get Function

![Get Function](/images/function-get.png){:class="img-fluid"}

#### Model class

![Model Class](/images/class-model.png){:class="img-fluid"}
