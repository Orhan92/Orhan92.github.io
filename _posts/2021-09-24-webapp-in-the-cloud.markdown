---
layout: post
title: "Web Application in the Cloud"
subtitle: "Lecture 6 - Web Application in the Cloud (Deadline 2021/09/24 23:55)"
date: 2021-09-24 15:43:11 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introcution

In this blog post we will create a ASP.NET Web application with Razor Pages.
The application is going to do the same things as the functions we created in the [Previous Blog Post](https://orhan92.github.io/jekyll/update/2021/09/22/database-in-the-cloud.html) with minor changes. Instead of giving a `searchterm` to get a specific artist we will instead retrieve every document inside of our database. Our application will establish a connection with our Cosmos Db where we can Post a song with a title or retrieve all documents inside of our database. Furthermore, we are going to add a Dockerfile that pushes our image to Azure's Container Registry. We can then pull our image down from ACR to run it on a container inside Docker Desktop. The difference between this blog post and our previous one is that we will now have an user interface on the web where we can fill in a form to push our content inside our CosmosDb and a listview of the documents stored inside of our CosmosDb.
