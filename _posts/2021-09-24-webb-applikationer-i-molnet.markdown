---
layout: post
title:  "Webb applikationer i molnet"
date:   2021-09-24 20:04:59 +0200
author: SvetlanaErn
---
En lösning för silvernivåövning.


[En applikation](https://seatemperature2.azurewebsites.net/) som jag byggde ger följande möjligheter:
1. Lägga till in DB mätningar av havstemperatur och tider av mätningar.
2. Få en lista med alla mätningar som lagras i DB.


<img src="/interface.jpg"/>


Applikationen var gjord i följande ordning:
1.	DB är SQL DB i Azure.
2.	Applikation är skriven med hjälp av RazorPages ASP.Net. 
3.	Skapa en docker image.
4.	Skapa Azure Container Registry (acr).
5.	Pusha image till acr. 
6.	Skapa App Service Web App i Azure som använder min docker image från acr.


Koppling mellan RazorPages och DB är gjord med hjälp av EntityFramework. Koden kan man hitta i [GitHub Repo](https://github.com/SvetlanaErn/RazorPagesTemperature). Den är ganska lätt och jag tror att förklaringar behövs inte. Applikationen består av en sida, se nedan koden till frontend:
```csharp
<div class="text-center">
    <h1 class="display-4">Havstemperatur</h1>
    <br/>
    <h2>Lägga till en ny mätning</h2>
    <br/>
    <form method="post">
        Datum och tid:
        <input asp-for="Input.DateTime" required>
        Temperatur (°C):
        <input asp-for="Input.Temperature" required>
        <p>@Model.ErrorMessage</p>
        <input type="submit" asp-page-handler="AddMeasurement" />
    </form>
    <br />

    <h2>Alla mätningar</h2>
    <table class="table">
        <thead>
            <tr>
                <th scope="col">Datum</th>
                <th scope="col">Temperatur</th>
            </tr>
        </thead>
        @foreach (var p in Model.MeasurementsList)
        {
            <tr>
                <td>@p.DateTime</td>
                <td>@p.Temperature</td>
            </tr>
        }
    </table>
</div>

```
Det finns två enkla metoder i PageModel:
```csharp
        public void OnGet()
        {
            MeasurementsList = DataAccess.GetMeasurements().Result;
        }

        public async Task<IActionResult> OnPostAddMeasurementAsync()
        {
            var response = await DataAccess.PostMeasurement(Input);
            return RedirectToPage("Index"); ;
        }
```
Data access lager: 
```csharp
 public class DataAccess
    {
        public static async Task<IActionResult> PostMeasurement(Measurement measurement)
        {
            try
            {
                await using var context = new MyContext();
                context.Measurements.Add(measurement);
                context.SaveChanges();
                return new OkObjectResult("success!");
            }
            catch
            {
                return new BadRequestObjectResult("something went wrong :(");
            }
        }

        public static async Task<List<Measurement>> GetMeasurements()
        {
            await using var context = new MyContext();
            List<Measurement> list = context.Measurements.ToList();
            return list;
        }
    }
```
Det finns problemet med säkerhet som jag inte kunde lösa. Det handlar om connection string till DB. För att gömma connection string till DB (för att kunna publicera koden på GitHub) använde jag Environment Variable (skrev detaljerad i [posten DB i molnet]( https://svetlanaern.github.io/2021/09/22/databaser-i-molnet.html)). Då fungerar inte applikationen om jag deploy det med hjälp av docker image och Azure App Service.


Kostnader att driva sådan apllikation. 
* om nästan ingen använadere finns:  60 $/månad
* om jätte jätte mycket använderefinns: 620 $/månad


*Källor:*


* [Azure Web App for Containers](https://www.youtube.com/watch?v=xnUOu-yPEzo)




