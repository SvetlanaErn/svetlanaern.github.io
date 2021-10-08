---
layout: post
title:  "Skalning upp och ut"
date:   2021-10-08 20:04:59 +0200
author: SvetlanaErn
---
**Skalning** är en process för att hantera resurser (t.ex. minne och CPU för VM, bandbredd, Request Units för Azure Cosmos DB) så att appen uppfyller prestandakrav.


**Skala upp/ner (vertikalt)** är en process där du ökar/minskar kapaciteten för en viss instans. 


Exampel på att skala upp:


1. VM: ökning från 1 virtuell processor och 3,5 GB RAM till 2 virtuella processorer och 7 GB RAM.
<img src="/Post_10_1.png"/>
2. Azure App Service: skala upp från App Service plan S1 (1 virtuell CPU och 1,75 GB RAM per instans) till S2 App Service plan (2 virtuella CPU och 3 GB RAM per instans).


**Skala ut/in (horisontellt)** - är en process där du ökar/minskar antal av instanser (t.ex. läggar till/ tar bort VM).
<img src="/Post_10_2.png"/>


Valet mellan horisontell och vertikal skalning beror på projektens karaktär. Om pålitlighet och lägre kostnader är viktigare än prestanda rekommenderas att skala projekten horisontellt. Om prestanda är viktigare rekommenderas att skala projekten vertikalt.


### Hur påverker det kostnad att ha en applikation Azure som skalar horisontalt vs vertikalt?
Vertikalt – priset fördubblas (ungefär) med varje steg upp. 
Horisontellt - priset stiger proportionellt antalet av instanser, dvs om man betalar t ex $10 för en instans ska man betala $20 för 2 instanser, $30 för 3 instanser osv.  


### Azure Service App plans och skalning:


<img src="/Post_10_3.jpg"/>


Skala upp/ner (vertikalt) – övergång från en App service plan till en annan plan med olika Core-RAM parametrar (t.ex. från S1 till S2 eller från B1 till S2). 
Skala ut/in (horisontellt) – ta fler/mindre instanser i samma service plan. Det är möjligt i Standart (upp till 10 instanser), Premium (upp till 30 instanser) och Isoleted (upp till 100 instanser) plans.


*Källor:*


* [Use scaling up and scaling out in your architecture](https://docs.microsoft.com/en-us/learn/modules/azure-well-architected-performance-efficiency/2-scaling-up-and-scaling-out)
* [App Servise pricing](https://azure.microsoft.com/en-us/pricing/details/app-service/windows/)
* [Scaling Up vs. Scaling Out: What’s the Difference?](https://pagely.com/blog/scaling-up-vs-scaling-out/)






