---
layout: post
title:  "Monitorering av molnapplikationer"
date:   2021-10-06 20:04:59 +0200
author: SvetlanaErn
---
För denna uppgift använde jag applikation från posten [”Webb applikationer i molnet”](https://svetlanaern.github.io/2021/09/24/webb-applikationer-i-molnet.html). Applikationen ger användaren följande möjligheter:
1. Lägga till in DB mätningar av havstemperatur och tider av mätningar.
2. Få en lista med alla mätningar som lagras i DB.
<img src="/interface.jpg"/>


För monitorering av applikationen lade jag till logging till Application Insights. Följande bild förklarar applikation arkitektur. Koden är skriven med hjälp av Razor Pages och deployas i Azure Web App Service. Data skrivs i Azure SQL DB. Loggarna sparas i Application Insights.   
<img src="/Post_9_1.png"/>


Monitorering är implementerad med hjälp av Serilog (packages Serilog.AspNetCore och Serilog.Sinks.ApplicationInsights). Program.cs filen ser ut så här:


```csharp
public static int Main(string[] args)
        {
            Log.Logger = new LoggerConfiguration()
            .MinimumLevel.Override("Microsoft", LogEventLevel.Information)
            .Enrich.FromLogContext()
            // for local
            .WriteTo.Debug()
            // for Azure Application Insights
            .WriteTo.ApplicationInsights(new TelemetryConfiguration { InstrumentationKey = "***" }, TelemetryConverter.Traces)
            .CreateLogger();

            try
            {
                Log.Information("Starting web host");
                CreateHostBuilder(args).Build().Run();
                return 0;
            }
            catch (Exception ex)
            {
                Log.Fatal(ex, "Host terminated unexpectedly");
                return 1;
            }
            finally
            {
                Log.CloseAndFlush();
            }
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .UseSerilog()
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
```


I RazorPages lade jag följande kod för monitorering (bara för att träna):

* till OnGet metod:
```csharp
Log.Information("Update page");
```
* till OnPost metod:
```csharp
Log.Information("New data: date {date}, temperature {temperature}", Input.DateTime, Input.Temperature);
```

### Exempel på queries:
Det första exemplet visar alla exceptions i 24 timmar. Detta information kan vara intressant för utvecklare för att öka app stabilitet.
<img src="/Post_9_2.jpg"/>
I det andra exemplet försökte jag få länder varifrån användare kommer. Men resultaten är konstig. När jag testade appen lokalt räknades dessa förfrågningar som svenska. Men när jag deployade koden till Azure räknades förfrågningar som amerikanska.   
<img src="/Post_9_3.jpg"/>


*Källor:*


* [Serilog.AspNetCore](https://github.com/serilog/serilog-aspnetcore#serilogaspnetcore---)





