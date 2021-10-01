---
layout: post
title:  "Filer i molnet"
date:   2021-10-01 20:04:59 +0200
author: SvetlanaErn
---
Lösningar för brons och silver nivåer.


Som jag förstod uppgiften krävs det att skapa två program. Det första programmet (Brons) är ett konsolprogram som ger möjligheten att välja en bild och ladda upp den till Azure Storage BlobContainer. Det andra programmet (Silver) är RazorPages ASP.Net app.  Den visar bilder som ligger i Azure Storage BlobContainer. Dataflöden kan man se på följande diagram:


<img src="/Post_8.jpg"/>


## Testa lokalt:


För att testa program lokalt använde jag Azurite emulator. Connection string ser ut så här:


```csharp
connectionString = "UseDevelopmentStorage=true"; 
```


Kommando för att starta emulator:


```console
azurite -l C:\Azurite
```


## Connection to Azure:


Connection string till Storage kan man hitta här: Azure Portal >>Storage Accounts >> Settings >> Access Keys >> Connection String (under key1)


Sedan skapade jag environment variable in a terminal:
```console
setx CONNECT_STR "[yourconnectionstringhere]"
```


Sedan i koden:
```csharp
connectionString = Environment.GetEnvironmentVariable("CONNECT_STR");
```


## Koden.


### Bronsnivå program:


```csharp
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);
            
string containerName = "image";
var containers = blobServiceClient.GetBlobContainers();
BlobContainerClient containerClient;
if (containers.Count(c => c.Name == containerName) == 0)
{
    // Create a new container if there is no one with this name
    containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName, PublicAccessType.Blob);
}
else
{
    // Get containerClient if conteiner exists 
    containerClient = blobServiceClient.GetBlobContainerClient(containerName);
}

// Choose a blob to upload
var images = ImageNames(); // get all names of the images in the folder "data"
//TODO: loop
var selectedOption = ShowMenu("Select an image to upload it to the container", images);
string fileName = images[selectedOption];
string localPath = "./data/";
string localFilePath = Path.Combine(localPath, fileName);

// Upload blob to a container
BlobClient blobClient = containerClient.GetBlobClient(fileName);
try
{
    using FileStream uploadFileStream = File.OpenRead(localFilePath);
    await blobClient.UploadAsync(uploadFileStream);
    uploadFileStream.Close();
    Console.WriteLine($"Uploading to Blob Storage:\n\t {blobClient.Uri}\n");
}
catch (Exception e) // for exampel - file with that name already exists
{
    Console.WriteLine(e.Message);
}

```


### Silvernivå program:


Anslut till BlobContainer och hämta blobs och Container URI 
```csharp
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);
BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);
            
blobs = containerClient.GetBlobs();
pathcontainer = containerClient.Uri;
```


Frontend delen:


```csharp
<h1 class="display-4">Images in the container "@Model.containerName":</h1>
    @foreach (var b in Model.blobs)
    {
        var path = @Model.pathcontainer + "/" + @b.Name;
        <img src="@path">
    }
```


## Prisundersökning:


Uppgiften:


Vad skulle det kosta att driva en applikation som spara och läser filer i Azure? Låt oss säga att man ska bygga en applikation som shutterstock. Vad skulle hända med kostnad över tid om du har 1000 använder som lägger upp 100 MB nya bilder varje dag (med din konsoll app), och alla bilder som finns i din blob laddas ner tre gångar per dag (med ditt web UI).


Lösning:


Varje dag adderas cirka 1000användare\*100 MB = 0.1 TB.


Efter ett år ska 36.5 TB lagras i storage.


Varje dag laddas ner (3gånger\*0.1TB\*dagNummer). Då dag 365 blir det cirka 110 TB.


Storage account (37 TB, West Europe, hot, LRS, Standart ) kostar 745 $/månad.


Bandwidth (Internet egress, 110 TB) kostar cirka 8400 $/månad.


Total Storage account + Bandwidth  = 745+8400 =9145$/månad.


Förbrukning stiger upp lineärt med dagar. Det känns som pris stiger upp lineärt med förbrukning.


Då pris för det första året blir 0,5\*12 månader\*9145$/månad =55000$ (ungefär)



*Källor:*


* [Azure Blob Storage using a .NET Core Console Application](https://medium.com/@rammonzito/azure-blob-storage-using-a-net-core-console-application-106a0c2e6de5)
* [Emulating Azure Storage with Azurite](https://medium.com/guestline-labs/emulating-azure-storage-with-azurite-743f6fbbf895)





