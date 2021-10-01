---
layout: post
title: "Files in the Cloud"
subtitle: "Lecture 8 - Networks in the Cloud (Deadline 2021/10/01 23:55)"
date: 2021-09-30 19:50:22 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

This blog post is all about working with Blobs and posting items inside of our blob containers. I will create a C# Console Application where we will establish a connection with our Azure blob storage which will hold our blob files and blob containers and a simple Web Application where we later can view the images inside our Azure application where we will publish our Web App. The goal is to push up a image locally to our blob storage and retrieve the URL to that image, we can then later copy the URL into our webbrowser and view the image OR enter our Web Application to view it. I have extended some functionality within my Console Application where you can store multiple images inside the same container, you can choose which container to work with and you can also create new containers to store images within.

So, we will have to create a Storage Account (in Azure portal), after that we are going to create a C# Console Application and within our application we are going to make it possible to create new containers inside our storage account, and push images to the container. We will have the possibility to create multiple containers or just a single container which will hold our images. We also need some error handling in order for our application to work properly. Mainly I will work with Try and Catch clausules and print out exception errors. As a last step we will create a Web App to view the images.

So what I have achieved so far is pretty good to be honest (I'm not bragging he he!). Of course, the application can get even better but due to lack of time I will leave it like it is. Improvement suggestions is: Create a more interesting and better UI to interact with, Make a Console Menu within the Console Application, Make the application keep running (and not exiting) until the user wants to exit (this can be configured with some sort of loop).

## The code

You can find the repository for the Console Application project here: [Blob Console App](https://github.com/Orhan92/BlobConsoleApp)
As you enter the repository you will notice that there are few files within the project. You will find all the logic code in [Program.cs](https://github.com/Orhan92/BlobConsoleApp/blob/master/Program.cs) file. There is also a [Image](https://github.com/Orhan92/BlobConsoleApp/tree/master/images) folder where I have stored local images in order to push them into a blob container.

You will notice that there are alot of comments in our code in order to help you understand exactly what's going on! Good right? ;)

Let us have a look at the code now.

![Main Method](/images/main.png){:class="img-fluid"}

Before I started coding I had to download the blob package: `dotnet add package Azure.Storage.Blobs`

In the beginning of the code I am setting my connection string to connect to our Azure Storage Account and then I print it out in order to make sure I have the right connection string: `string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");` followed by `Console.WriteLine(connectionString);`. I did this by configuring my connection string with a environment variable. In order to do this I had to type this command: `setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"` in my powershell.

Next step was to connect to my `BlobServiceClient` with the help of my connection string: `BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);`. The ``BlobServiceClient` allows us to manipulate our Azure Storage service resources and blob containers. In this case it would be for our Blob containers. We will need this in order to work with our blobs.

Next part was to create a container, which we do inside `CreateSampleContainerAsync()`-method.

![Create Container](/images/create-container.png){:class="img-fluid"}

This method will let the user enter a container name (has to be lower case letters). After that, the `BlobServiceClient` will attempt to create a container with the given name from the user. If the container already exists, the program will tell us that this container already exists. If the container doesn't exist, our method will create a container with the user input name. Right after that we, our Console Application will show a list of active containers inside our blob storage with the `ListContainers()`-method which can be seen in the `Main`-method on Row 33.

![List Containers](/images/container-list.png){:class="img-fluid"}

So if the container was created and didn't exist before the user created it, we will jump into the `else block` inside our `Main`-method (Row 67) where we will upload the Blob Image inside the newly created container.

![List Containers](/images/create-image.png){:class="img-fluid"}

So, there are some flaws inside this method which we defenitely could improve. The first flaw is that we are hardcoding the image name and path in order to upload it to our Blob Storage. The second flaw is that we cannot upload a image with the same name as we are not generating new random Guids. This can also be improved and modified in order to upload same image multiple times.

So the steps above happens when there are no corresponding containers to the user input. But what happens when there is a existing container corresponding to the user input? Lets go back to our `Main`-method and head to Row 36. So what happens here is that the `blobServiceClient` on row 27 will hold a `null` value if the container already exists. Therefore, the logic is that, whenever `blobServiceClient` is `null` (There is already a existing container), then we jump inside our If-block. Since we have already listed our existing containers on Row 33 with the `ListContainers()` method, the first thing we ask the user in our If-block is which container he/she wants to work with. Then we get the container the user wants to work with on Row 43 with the help of `GetContainer()`-method.

![Get Container](/images/get-container.png){:class="img-fluid"}

Inside this method we are creating two new lists which contains Blob Items (containers). I will reference to the comments in the image above in order for you to understand why we created two lists. Then in the end of the method we will return the container that the user want to work with. If we cannot find any match then we will return null. If null was returned then we will print a message to the user telling them we cannot find a corresponding container with the input they gave. If a blob item was returned, then we will create and upload the image inside that particular container. This will let us upload many items/images inside the same container multiple times. But as I mentioned about the flaws when uploading a image, if the image name is the same then we will not duplicate that image, instead our code will replace the old image in the container with the new one. And the last thing we do in our Console Application is to print every item/image URL inside the container we are working with.

#### How it looks like in the portal

![Azure](/images/azure-storage.png){:class="img-fluid"}

### Web Application

Regarding the Web Application, I do not have a git repo for it but I will illustrate and show how it works. Reson for this is that the project folder somehow had a conflict with the Main repo (The Console Application) and due to lack of time I will not focus on solving this problem. Instead I will illustrate how it works. Here is a print of what the entire solution looks like:

![Solution](/images/solution.png){:class="img-fluid"}

You can see that we use a Console Application And a Web Application for this solution.

The Web Application is very simple as I only added a `Images` page and that's it. Lets have a look at what the `Images.cshtml.cs` file does

![Images Class](/images/images-class.png){:class="img-fluid"}

We Establish a connection with our Storage Account here which can be found in our `appsettings.json` file. Later on we are telling our Page to look for a container named `images` and add the Uri for every blob inside of the container. Inside of our `Images.cshtml` file we will show all of our images based on the Uri from our images inside our Blob Container.

<pre class="csharp" style="font-family:monospace;">@<span style="color: #0600FF; font-weight: bold;">foreach</span> <span style="color: #008000;">&#40;</span><span style="color: #0600FF; font-weight: bold;">var</span> uri <span style="color: #0600FF; font-weight: bold;">in</span> Model<span style="color: #008000;">.</span><span style="color: #0000FF;">allBlobs</span><span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
    <span style="color: #008000;">&lt;</span>img src<span style="color: #008000;">=</span><span style="color: #666666;">&quot;@uri&quot;</span> <span style="color: #008000;">/&gt;</span>
<span style="color: #008000;">&#125;</span></pre>

## Illustration of how the application works

#### Lets have a look at how the Console Application works

![Console Illustration](/images/console-illustration.png){:class="img-fluid"}

You can see exactly how the Console Application works (As I have described with words above). We start with typing in a name for the container we want to create. The application responds with a Code 409 `ContainerAlreadyExists`. Then we list all available containers. Our Application asks us to type in which container we would like to work with and lists every Blob URL within that container. We can now litteraly copy one URL into our web browser and the image will be displayed.

#### Lets have a look at how the Web Application works

As I have published this Web Application into Azure App Services, lets navigate to our Application inside Azure to see what it looks like. Resource Group -> App Service -> Select the web application -> Get the URL -> Paste URL in Web Browser.

![WebApp Illustration](/images/web-images.png){:class="img-fluid"}

As you can see we are printing every image inside the "images" container in our web application.

### Conclusion

This project uses a Console Application in order to create blob containers and push images to them. Then we use a Web Application to display our Images inside our Blob Container. This is just a first draft. Of course we could improve many aspects of this project such as.

**Console**:

- Add a Menu
- Loop the application until user decides to exit, this was the user can add as many pictures as he/she wants
- Remove the hardcoded image upload and let the user decide which image to upload
- Add Guid to the image uploads so that we can upload duplicates if we want to
- Set a fixed amount of how many items a container can hold until we have to create a new one

**Web App**:

- Work more with the UI
- Make it more beautiful
- Let the user decide which container to get the images from

As you can see there are alot to work with but for now we have a working Console and Web application.

## Diagram of the application

![Diagram](/images/diagram-blob.jpg){:class="img-fluid"}

## What would it cost to have a application in the clouds that can save and read images from Blob Storage?

Lets say we use Azure Storage (Blob) in order to post new images and retrieve them. Lets say we post 100 MB images each day and every image inside our container will be retrieved 3 times/day. If we use Storage Acoount with Block Blob Storage types, Premium performance, North Europe as a location, Zone-redundant storage and 1000 GB capacity on our Storage Acoount, then we will approximately have to pay around 100$/month.

## How does Microsoft Secure our blob data

There are alot to say here but the main encryption and security Azure uses is to work with a Azure Key Vault and Encryption Keys. All keys are stored in a vault and can then for example be invoked by URI. Microsoft have also designed their Key Vault so that noone except you can see/and extract those keys. So the best and secure way, in my opinion, is that Microsoft uses encrypted Key Vaults which can only be accessed by for example the storage owner. The owner can set permissions on which kind of access the public can have and so on. There are alot to say about how Microsoft secures our blob data, but I think this is one of the bigger solutions that Microsoft uses in order to secure data.

#### References

[Create a Storage Account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)\
[Create Container and Upload Blob](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal)\
[Upload Image blobs](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-upload-process-images?tabs=dotnet%2Cazure-powershell)\
[Connect Application with Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-dotnet)\
[Create a Container](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-container-create?tabs=dotnet)\
[Set content type of Blob](https://stackoverflow.com/questions/10040403/set-content-type-of-media-files-stored-on-blob)\
[Azure Encryption Blob Data](https://cloudacademy.com/blog/how-does-azure-encrypt-data/)
