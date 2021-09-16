---
layout: post
title:  "Severless applikationer"
date:   2021-09-16 20:04:59 +0200
author: SvetlanaErn
---
## Vad är Serverless och Function As A Service (FaaS)?
Serverless computing är en strategi för att organisera molntjänster, där molnet automatiskt hanterar fördelningen av datorresurser beroende på användarbelastningen. Huvuduppgiften för sådan strategi är genomförandet av mallen Function As A Service (FaaS), där en container eller virtuell maskin skapas för varje anrop, som förstörs efter körning.


Namnet beror på det faktum att användarna i denna strategi inte behöver hantera fördelning och konfiguration av servrar: alla inställningar och planering av datorresurser är dolda från användaren och hanteras av molnet.
## Exempel: simpel kalkylator
Funktionen triggas när det får http anrop, får två tal från body och returnerar deras summa. För tillgång krävs en nyckel.
Funktionen var byggd i Azures webgränssnitt och testad med hjälp av Postman. För att öka säkerheten av funktionen används try-catch block.

``` C#
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public class TwoNumbers
{
    public string n1;
    public string n2;
}
public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        TwoNumbers data = JsonConvert.DeserializeObject<TwoNumbers>(requestBody);
        float number1 = float.Parse(data.n1);
        float number2 = float.Parse(data.n2);
        return new OkObjectResult($"{number1}+{number2} = {(number1+number2)}");
    }
    catch
    {
        return new BadRequestObjectResult("something went wrong");
    }
}

```
<img src="/azureFunction.jpg"/>


*Källor:*


* [Understanding Serverless Computing for the Beginner](https://geekflare.com/know-about-serverless/)
* [Execute an Azure Function with triggers](https://docs.microsoft.com/en-us/learn/modules/execute-azure-function-with-triggers/)



