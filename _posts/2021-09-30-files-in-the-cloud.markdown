---
layout: post
title: "Files in the Cloud"
subtitle: "Lecture 8 - Networks in the Cloud (Deadline 2021/10/01 23:55)"
date: 2021-09-30 19:50:22 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

This blog post is all about working with Blobs and posting items inside of our blob containers. I will create a C# Console Application where we will establish a connection with our Azure blob storage which will hold our blob files and blob containers. The goal is to push up a image locally to our blob storage and retrieve the URL to that image (I will not create a Web Application with UI for this, but I will show you how it works within the Console Application). I have extended some functionality within my Console Application where you can store multiple images inside the same container, you can choose which container to work with and you can also create new containers to store images within.

So, we will have to create a Storage Account (in Azure portal), after that we are going to create a C# Console Application and within our application we are going to make it possible to create new containers inside our storage account, and push images to the container. We will have the possibility to create multiple containers or just a single container which will hold our images. We also need some error handling in order for our application to work properly. Mainly I will work with Try and Catch clausules and print out exception errors.

So what I have achieved so far is pretty good to be honest (I'm not bragging he he!). Of course, the application can get even better but due to lack of time I will leave it like it is. Improvement suggestions is: Create UI to interact with, Make a Console Menu within the Console Application, Make the application keep running (and not exiting) until the user wants to exit (this can be configured with some sort of loop). Otherwise I think we will fulfill every criteria for this exercise and more..

## The code

You can find the repository for this project here: [Blob Console App](https://github.com/Orhan92/BlobConsoleApp)
As you enter the repository you will notice that there are few files within the project. You will find all the logic code in [Program.cs](https://github.com/Orhan92/BlobConsoleApp/blob/master/Program.cs) file. There is also a [Image](https://github.com/Orhan92/BlobConsoleApp/tree/master/images) folder where I have stored local images in order to push them into a blob container.

You will notice that there are alot of comments in our code in order to help you understand exactly what's going on! Good right? ;)

Let us have a look at the code now.

![Main Method](/images/main.png){:class="img-fluid"}

Before I started coding I had to download the blob package: `dotnet add package Azure.Storage.Blobs`

In the beginning of the code I am setting my connection string to connect to our Azure Storage Account and then I print it out in order to make sure I have the right connection string: `string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");` followed by `Console.WriteLine(connectionString);`. I did this by configuring my connection string with a environment variable. In order to do this I had to type this command: `setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"` in my powershell.

Next step was to connect to to my `BlobServiceClient` with the help of my connection string: `BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);`. The ``BlobServiceClient` allows us to manipulate our Azure Storage service resources and blob containers. In this case it would be for our Blob containers.
