---
layout: post
title:  "Databaser i molnet"
date:   2021-09-22 20:04:59 +0200
author: SvetlanaErn
---
En lösning för bronsnivåövning.


Applikationen som jag byggde är ett REST API. Det hanterar två api-anrop:


POST: för att lägga till data (tid + temperatur) till DB.
<img src="/post.jpg"/>


GET:  för att få alla data (en lista med par värden: tid och temperatur) från DB.
<img src="/get.jpg"/>


API är gjord med Azure Functions. Databasen är SQL Server i Azure dvs serverless.  


Applikationen var gjord i följande ordning:
1. Skapa SQL DB i Azure.
2. Bygga Azure Functions lokalt i VS. Koppla DB som ligger i molnet, köra migrations.
3. Ladda koden till GitHub Repo.
4. Deploy koden från GitHub Repo till Azure Functions.

## Koden

**DB** innehåller en tabell med kolumner ID, DateTime och Temperature. Klassen som motsvarar tabellen heter Measurement:
```csharp
 public class Measurement
    {
        [Key]
        public int ID { get; set; }
        [Required]
        public DateTime DateTime { get; set; }
        [Required]
        public float Temperature { get; set; }
    }
```


**Connection** mellan Azure functions och DB är byggd med hjälp av EntityFramework (obs: EntityFramework Design och SQL Server v3, annars strular det). Connection string till DB kan man hitta: Azure portal-> SQL Database-> namn DB-> overview-> Connection strings -> connection strings.
```csharp
  public class MyContext : DbContext
    {
        public DbSet<Measurement> Measurements { get; set; }
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(Environment.GetEnvironmentVariable("SQLAZURECONNSTR_DB"));
        }
    }
```
För att gömma login och lösenord till DB använde jag Environment Variable som heter SQLAZURECONNSTR_DB (VS ->Debug -> SolutionNamn Debug Properties-> Environment Variable) som innehåller connection string med lösenord. Då läggs till en fil till projekten Properties->launchSettings.json som behövs lägga till gitignor.


**Post** metod som får data från body och lägger till dem i DB:
```csharp
 public static class PostMeasurement
    {
        [FunctionName("PostMeasurement")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            try
            {
                string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
                Measurement data = JsonConvert.DeserializeObject<Measurement>(requestBody);
                if (data.DateTime == DateTime.MinValue) return new BadRequestObjectResult("date missing");

                await using var context = new MyContext();
                context.Measurements.Add(data);
                context.SaveChanges();
                return new OkResult();
            }
            catch
            {
                return new BadRequestObjectResult("wrong format");
            }
        }
    }
```


**Get** metod som returnerar alla data från DB:
```csharp
    public static class GetMeasurements
    {
        [FunctionName("GetMeasurements")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            await using var context = new MyContext();
            List<Measurement> list = context.Measurements.ToList();
            return new OkObjectResult(list);
        }
    }
```


För att **deploy** koden från GitHub Repo till Azure behövs göra följande i PowerShell:
1. Logga in till Azure: az login
2. Deploy


Function app and storage account names must be unique.
```console
$Random = Get-Random -Maximum 100000
$storageName="CHANGE_Name$RANDOM"
$functionAppName="CHANGE_Name$RANDOM"
$ResourceGroupName = "CHANGE_Name$RANDOM" 
$region="westeurope"

$gitrepo="https://github.com/SvetlanaErn/AzureFunctionsAPISQL.git"
$token="CHANGE_GitHubToken"
```
Enable authenticated git deployment in your subscription from a private repo.
```console
az functionapp deployment source update-token --git-token $token
```
Create a resource group.
```console
az group create  --name $ResourceGroupName --location $region
```
Create an Azure storage account in the resource group.
```console
az storage account create --name $storageName --location $region --resource-group $ResourceGroupName --sku Standard_LRS
```
Create a function app with source files deployed from the specified GitHub repo.
```console
az functionapp create --name $functionAppName `
  --storage-account $storageName `
  --consumption-plan-location $region `
  --resource-group  $ResourceGroupName `
  --deployment-source-url $gitrepo `
  --deployment-source-branch main `
  --functions-version 3
```


Sedan behövs det att lägga till Connection string to DB i Azure Functions: name API->Settings, configurations -> Connection strings-> New connection string (Name = DB, om EnvironmentVariable = “SQLAZURECONNSTR_DB”).


**Hur har du tänkt runt uppdatring av databsen ifall scheman ändras? Migrations?**


Nu behövs det att köra migrations manuellt. Jag tror att en bättre lösning existerar.


**Vad skulle det kosta att driva detta?**


Två scenarier:
* Nästan ingen använadere: 5$/månad
* Jätte jätte mycket användere: 465$/månad


*Källor:*


* []()




