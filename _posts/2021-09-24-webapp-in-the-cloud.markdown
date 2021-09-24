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
The application is going to do the same things as the functions we created in the [Previous Blog Post](https://orhan92.github.io/jekyll/update/2021/09/22/database-in-the-cloud.html) with minor changes. Instead of giving a `searchterm` to get a specific artist we will instead retrieve every document inside of our database. Our application will establish a connection with our Cosmos Db where we can Post a song with a title or retrieve all documents inside of our database. Furthermore, we are going to add a Dockerfile that pushes our image to Azure's Container Registry. We can then pull our image down from ACR to run it on a container inside Docker. The difference between this blog post and our previous one is that we will now have an user interface on the web where we can fill in a form to push our content inside our CosmosDb and a listview where we can get all the documents stored inside of our CosmosDb. I will go through these steps later on.

### OBSERVE

All of the Keys and other sensitive data in this section will be deleted and removed in order to avoid abuse against the resource group. These are created and made for educational purpose only.

## The code and how the Web App works

So in order to build my web application with Razor Pages, I followed a [Microsoft Docs](https://docs.microsoft.com/en-us/azure/cosmos-db/sql/sql-api-dotnet-application#create-a-new-mvc-application) documentation with some tweaks as that documentation was provided to create a ASP.NET Core Application with MVC and controllers. In our case, we are using Razor Pages. This is how I achieved to setup my Razor Pages and connect it to my CosmosDb in Azure portal. So that documentation is a good link to follow as you read through this blog post.

After I created my new Razor Pages project, I had to install `Microsoft.Azure.Cosmos` extension from NuGet packages.

Next step was to create a Model (see [GitHub Repo](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Models/Song.cs)) that holds the same properties as the ones we have inside our CosmosDb, this so we can post and retrieve data documents from our database. As you can see inside the repository link above, I have set a Json property to all of the class properties: `[JsonProperty(PropertyName = "id")]` as an example. This makes sure that all the property names that we push up/or retrieve are unified and has the same value (id, artist, title, timeCreated) whenever we work with the model. Notice that we always work with lowercase letters.

Now that we have our Model class set, I knew that we needed some way to connect/talk to our CosmosDb. In order to achieve this I had to use dependency injection, declare and initialize servives for our project.

My first step was to create a new folder called [Services](https://github.com/Orhan92/RazorPagesAzureWebApp/tree/master/Services). Inside of this folder I added an [interface](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Services/ICosmosDbService.cs) together with a [CosmosDbService class](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Services/CosmosDbService.cs).
My interface, at this point, only contains of two tasks: one to get all the documents from the database and one to create an item. I have also commented out three other tasks (GetItem by Id, Update Item and Delete Item). These other three can be used to keep on building on the application, which can be seen in the `CosmosDbService.cs` class aswell. But for now we will stay happy with a Get and Post. See code snippet:

<pre class="csharp" style="font-family:monospace;"><span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">CosmosWebApp.Models</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Collections.Generic</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Linq</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Threading.Tasks</span><span style="color: #008000;">;</span>
&nbsp;
<span style="color: #0600FF; font-weight: bold;">namespace</span> CosmosWebApp
<span style="color: #008000;">&#123;</span>
    <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #6666cc; font-weight: bold;">interface</span> ICosmosDbService
    <span style="color: #008000;">&#123;</span>
        Task<span style="color: #008000;">&lt;</span>IEnumerable<span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;&gt;</span> GetItemsAsync<span style="color: #008000;">&#40;</span><span style="color: #6666cc; font-weight: bold;">string</span> query<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        Task AddItemAsync<span style="color: #008000;">&#40;</span>Song song<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        <span style="color: #008080; font-style: italic;">//Task&lt;Song&gt; GetItemAsync(string id);</span>
        <span style="color: #008080; font-style: italic;">//Task UpdateItemAsync(string id, Song song);</span>
        <span style="color: #008080; font-style: italic;">//Task DeleteItemAsync(string id);</span>
    <span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span></pre>

When the Interface was created it was time to create the `CosmosDbService` class to connect with our Azure Cosmos Db. This Service will do the CRUD operations later on inside of our pagemodels.

See class below:

<pre class="csharp" style="font-family:monospace;"><span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Collections.Generic</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Linq</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">System.Threading.Tasks</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">CosmosWebApp.Models</span><span style="color: #008000;">;</span>
<span style="color: #0600FF; font-weight: bold;">using</span> <span style="color: #008080;">Microsoft.Azure.Cosmos</span><span style="color: #008000;">;</span>
&nbsp;
<span style="color: #0600FF; font-weight: bold;">namespace</span> CosmosWebApp<span style="color: #008000;">.</span><span style="color: #0000FF;">Services</span>
<span style="color: #008000;">&#123;</span>
    <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #6666cc; font-weight: bold;">class</span> CosmosDbService <span style="color: #008000;">:</span> ICosmosDbService
    <span style="color: #008000;">&#123;</span>
        <span style="color: #0600FF; font-weight: bold;">private</span> Container _container<span style="color: #008000;">;</span>
&nbsp;
        <span style="color: #0600FF; font-weight: bold;">public</span> CosmosDbService<span style="color: #008000;">&#40;</span>
            CosmosClient dbClient,
            <span style="color: #6666cc; font-weight: bold;">string</span> databaseName,
            <span style="color: #6666cc; font-weight: bold;">string</span> containerName<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            <span style="color: #0600FF; font-weight: bold;">this</span><span style="color: #008000;">.</span>_container <span style="color: #008000;">=</span> dbClient<span style="color: #008000;">.</span><span style="color: #0000FF;">GetContainer</span><span style="color: #008000;">&#40;</span>databaseName, containerName<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span>
&nbsp;
        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task AddItemAsync<span style="color: #008000;">&#40;</span>Song song<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            <span style="color: #0600FF; font-weight: bold;">await</span> <span style="color: #0600FF; font-weight: bold;">this</span><span style="color: #008000;">.</span>_container<span style="color: #008000;">.</span><span style="color: #0000FF;">CreateItemAsync</span><span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;</span><span style="color: #008000;">&#40;</span>song, <span style="color: #008000;">new</span> PartitionKey<span style="color: #008000;">&#40;</span>song<span style="color: #008000;">.</span><span style="color: #0000FF;">Id</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span>
        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task<span style="color: #008000;">&lt;</span>IEnumerable<span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;&gt;</span> GetItemsAsync<span style="color: #008000;">&#40;</span><span style="color: #6666cc; font-weight: bold;">string</span> queryString<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            <span style="color: #0600FF; font-weight: bold;">var</span> query <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">this</span><span style="color: #008000;">.</span>_container<span style="color: #008000;">.</span><span style="color: #0000FF;">GetItemQueryIterator</span><span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">new</span> QueryDefinition<span style="color: #008000;">&#40;</span>queryString<span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            List<span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;</span> results <span style="color: #008000;">=</span> <span style="color: #008000;">new</span> List<span style="color: #008000;">&lt;</span>Song<span style="color: #008000;">&gt;</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #0600FF; font-weight: bold;">while</span> <span style="color: #008000;">&#40;</span>query<span style="color: #008000;">.</span><span style="color: #0000FF;">HasMoreResults</span><span style="color: #008000;">&#41;</span>
            <span style="color: #008000;">&#123;</span>
                <span style="color: #0600FF; font-weight: bold;">var</span> response <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">await</span> query<span style="color: #008000;">.</span><span style="color: #0000FF;">ReadNextAsync</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
                results<span style="color: #008000;">.</span><span style="color: #0000FF;">AddRange</span><span style="color: #008000;">&#40;</span>response<span style="color: #008000;">.</span><span style="color: #0000FF;">ToList</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #008000;">&#125;</span>
            <span style="color: #0600FF; font-weight: bold;">return</span> results<span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span>
    <span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span></pre>

This class is the logic for all the CRUD operations against our CosmosDb. It is inside this class we will retrieve access to our CosmosDb from our [Startup.cs](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Startup.cs).

Lets have a look at our injected dependencies inside of our Startup class. So the first thing we do here is to create a new method which will initialize an instance of our cosmos db client. This is how we will establish contact with our database once we run our web application.
The code below is added as a new method inside our Startup class.

<pre class="csharp" style="font-family:monospace;">        <span style="color: #0600FF; font-weight: bold;">private</span> <span style="color: #0600FF; font-weight: bold;">static</span> <span style="color: #0600FF; font-weight: bold;">async</span> Task<span style="color: #008000;">&lt;</span>CosmosDbService<span style="color: #008000;">&gt;</span> InitializeCosmosClientInstanceAsync<span style="color: #008000;">&#40;</span>IConfigurationSection configurationSection<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            <span style="color: #6666cc; font-weight: bold;">string</span> databaseName <span style="color: #008000;">=</span> configurationSection<span style="color: #008000;">.</span><span style="color: #0000FF;">GetSection</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;DatabaseName&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0600FF; font-weight: bold;">Value</span><span style="color: #008000;">;</span>
            <span style="color: #6666cc; font-weight: bold;">string</span> containerName <span style="color: #008000;">=</span> configurationSection<span style="color: #008000;">.</span><span style="color: #0000FF;">GetSection</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;ContainerName&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0600FF; font-weight: bold;">Value</span><span style="color: #008000;">;</span>
            <span style="color: #6666cc; font-weight: bold;">string</span> account <span style="color: #008000;">=</span> configurationSection<span style="color: #008000;">.</span><span style="color: #0000FF;">GetSection</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;Account&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0600FF; font-weight: bold;">Value</span><span style="color: #008000;">;</span>
            <span style="color: #6666cc; font-weight: bold;">string</span> key <span style="color: #008000;">=</span> configurationSection<span style="color: #008000;">.</span><span style="color: #0000FF;">GetSection</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;Key&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0600FF; font-weight: bold;">Value</span><span style="color: #008000;">;</span>
            Microsoft<span style="color: #008000;">.</span><span style="color: #0000FF;">Azure</span><span style="color: #008000;">.</span><span style="color: #0000FF;">Cosmos</span><span style="color: #008000;">.</span><span style="color: #0000FF;">CosmosClient</span> client <span style="color: #008000;">=</span> <span style="color: #008000;">new</span> Microsoft<span style="color: #008000;">.</span><span style="color: #0000FF;">Azure</span><span style="color: #008000;">.</span><span style="color: #0000FF;">Cosmos</span><span style="color: #008000;">.</span><span style="color: #0000FF;">CosmosClient</span><span style="color: #008000;">&#40;</span>account, key<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            CosmosDbService cosmosDbService <span style="color: #008000;">=</span> <span style="color: #008000;">new</span> CosmosDbService<span style="color: #008000;">&#40;</span>client, databaseName, containerName<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            Microsoft<span style="color: #008000;">.</span><span style="color: #0000FF;">Azure</span><span style="color: #008000;">.</span><span style="color: #0000FF;">Cosmos</span><span style="color: #008000;">.</span><span style="color: #0000FF;">DatabaseResponse</span> database <span style="color: #008000;">=</span> <span style="color: #0600FF; font-weight: bold;">await</span> client<span style="color: #008000;">.</span><span style="color: #0000FF;">CreateDatabaseIfNotExistsAsync</span><span style="color: #008000;">&#40;</span>databaseName<span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            <span style="color: #0600FF; font-weight: bold;">await</span> database<span style="color: #008000;">.</span><span style="color: #0000FF;">Database</span><span style="color: #008000;">.</span><span style="color: #0000FF;">CreateContainerIfNotExistsAsync</span><span style="color: #008000;">&#40;</span>containerName, <span style="color: #666666;">&quot;/id&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
&nbsp;
            <span style="color: #0600FF; font-weight: bold;">return</span> cosmosDbService<span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span></pre>

After I added the class above into our Startup class, I had to add my dependency injection for services:

<pre class="csharp" style="font-family:monospace;">        <span style="color: #0600FF; font-weight: bold;">public</span> <span style="color: #6666cc; font-weight: bold;">void</span> ConfigureServices<span style="color: #008000;">&#40;</span>IServiceCollection services<span style="color: #008000;">&#41;</span>
        <span style="color: #008000;">&#123;</span>
            services<span style="color: #008000;">.</span><span style="color: #0000FF;">AddRazorPages</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
            services<span style="color: #008000;">.</span><span style="color: #0000FF;">AddSingleton</span><span style="color: #008000;">&lt;</span>ICosmosDbService<span style="color: #008000;">&gt;</span><span style="color: #008000;">&#40;</span>InitializeCosmosClientInstanceAsync<span style="color: #008000;">&#40;</span>Configuration<span style="color: #008000;">.</span><span style="color: #0000FF;">GetSection</span><span style="color: #008000;">&#40;</span><span style="color: #666666;">&quot;CosmosDb&quot;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0000FF;">GetAwaiter</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">.</span><span style="color: #0000FF;">GetResult</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">;</span>
        <span style="color: #008000;">&#125;</span></pre>

Now that we have added all the necessary code into our `Startup.cs` file to establish contact with our Cosmos Db, we have to adjust our [appsettings.json](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/appsettings.json) file. It is inside this file that we add our database connection strings. So, before we can do that we have to head into our Azure portal and copy our database `URI` and `PRIMARY KEY`:

![DB-Keys](/images/db-keys.png){:class="img-fluid"}

Once you have retrieved the `URI` and `PRIMARY KEY`, you have to insert it into your `appsettings.json`. See [appsettings.json](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/appsettings.json) for illustration.

At this point we have a connection with our database inside of our web application. Now it is time to work with our Razor Pages.

### Pages and Pagemodels

In this project we only have two pages and pagemodels which we added into the project. The first one is [Create.cshtml](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Pages/Create.cshtml) which will be the (frontend) interface for the user together with [Create.cshtml.cs](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Pages/Create.cshtml.cs) which is the backend functions and where we have the logic to push data into our database. The user have to fill in an artist name together with a title in order to push the song into our CosmosDb. See links above for detailed view of the code.

The second page is [Get.cshtml](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Pages/Get.cshtml) where we print a list of all the song documents inside of our Cosmos Database. The pagemodel can be found here: [Get.cshtml.cs](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Pages/Get.cshtml.cs). This is where we retrieve data from our database and present it to the user. We are also using SQL query to retrieve relevant information inside this method: `var result = await _cosmosDbService.GetItemsAsync("SELECT c.id, c.artist, c.title, c.timeCreated FROM c ORDER BY c.artist");`.

As you look through the page models, notice that we are using our Service classes where we also operate the CRUD operations. We mentioned about the service class and interface before. So this might then be familiar to you. You can find the link to our service class here: [CosmosDbService.cs](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Services/CosmosDbService.cs).

### Dockerfile

We also have our Dockerfile so that we can push our application image into Azure Container Registry. And thereafter we can always pull that image and run our application or let another developer pull that image and run it on his machine. In order to achieve to push our image to Azure Container Registry, I had to go throught this [Documentation](https://blog.hildenco.com/2020/10/pushing-docker-images-to-azure.html) where you will learn how to create a `Container Registry`, `Push` and `Pull` the image created.

See my [Dockerfile](https://github.com/Orhan92/RazorPagesAzureWebApp/blob/master/Dockerfile).

Just to double check that everything have been pushed into our Container Registry: We can always go to our Azure Portal, navigate to our created Container Registry and then click on `Repositories` in the left navigation bar.

![Container Registry](/images/container-registry.png){:class="img-fluid"}

Now that we know that our docker image is up in `Azure Container Registry`, we can navigate our way into the image and see how to pull that image. In my case it would be to run this command: `docker pull razorpagescosmosdb.azurecr.io/musicapp:v1`. But before we do that I have to login to my azure account and provide that ACR name `az acr login --name <your-acr-name>`. After that we can run the `docker pull` command and voíla, we just pulled our image from the container registry.

## Publishing Azure App Service

Now that we have our application working with our Cosmos Db and our Dockerfile ready it is time to deploy the app to Azure App Service. To do so I followed this [Documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/sql/sql-api-dotnet-application#deploy-the-application-to-azure) where you will receive a step-by-step manual on how to publish your application. But in short, what I did was to rightclick the project in **Solution Explorer** and selected **Publish**.

You will then have to fill in information required in order to publish your application. If everything succeeds, you will then be able to see, use and run your application from Azure App Services. Below, I will show a demonstration of what my application looks like and how it works now that it is published in App Services.

#### Create/Post

![Create document](/images/create-cloud.png){:class="img-fluid"}

#### Get/Retrieve

![Get document](/images/get-cloud.png){:class="img-fluid"}

## Estimated costs

#### With very few users

With very few users, where we pay for a Cosmos Db and App Services, this is how a estimated cost could look like:

![Few users](/images/est-few-app.png){:class="img-fluid"}

#### With many users

With many users, where we pay for a Cosmos Db and App Services, this is how a estimated cost could look like:

![Few users](/images/est-alot-app.png){:class="img-fluid"}

#### References

[ASP.NET, CosmosDb and Deployment](https://docs.microsoft.com/en-us/azure/cosmos-db/sql/sql-api-dotnet-application#deploy-the-application-to-azure)/
[Push Docker images to Azure](https://blog.hildenco.com/2020/10/pushing-docker-images-to-azure.html)/
[Pricing Calculator](https://blog.hildenco.com/2020/10/pushing-docker-images-to-azure.html)
