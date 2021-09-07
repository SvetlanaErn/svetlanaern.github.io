---
layout: post
title:  "Vad är molnet?"
date:   2021-09-07 13:04:59 +0200
author: SvetlanaErn
---
**Molnet** är en jättestor ”datorcenter” i den meningen att det består av servrar, lagringsutrymme, nätverk osv. Men till skillnad från ett vanligt datacenter är virtualliseringskomponenter dolda från administrator, förutom erbjuder molnet olika tjänstmodeller beroende på kundens behov. Tillsammans med mjukvara ger molnet följande **möjligheter**:   
* Att koppla till tjänsten och få service är lätt och snabbt.
* Tillgänglighet via standardplattformar (desktop, laptop, mobile, etc.)
* Kunder delar resurser 
* Att lägga till/ta bort resurser, justera prestandanivå på befintliga resurser
* Betalning som månads/tim-avgift för förbrukade tjänster


**Det finns tre molntjänstmodeller**: 
* Infrastruktur som en tjänst (molntjänsten ta hand om fysiska servrar)
* Plattform som en tjänst (molntjänsten ta hand om fysiska servrar, operativt system och databaser)
* Programvara som en tjänst (molntjänsten ta hand om fysiska servrar, operativt system, databaser och applikationer)


# Vilka fördelar och nackdelar kan molnet introducera?
### Fördelar:
* Hög tillgänglighet: kontinuerlig användarupplevelse utan märkbara avbrott, även om någonting går fel. 
* Skalbarhet och elasticitet: det är möjligt snabbt att lägga till eller ta bort RAM-minne, processorer eller virtuella datorer.
* Geo-distribution: du kan distribuera appar och data till regionala datacenter över hela världen för bättre prestanda.
* Haveriberedskap
* Månadsavgift för dessa resurser som man faktiskt använder. Det behövs inte att köpa dyra servrar.
* Driftavdelningen behöver inte ta ansvar för hårdvaran, reservdelar osv


### Nackdelar:
* Säkerhet vid lagring och överföring av data
* Beroende på infrastruktur mellan molnet och användaren
* Prestanda när man arbetar med data i molnet kan vara lägre än när man arbetar med lokala kopior av data
* Månadsavgift på lång sikt kan bli dyrare än att köpa servrar själv. 


# Prisundersökning.
Prisjämförelse för att drifta en server (24x7) under ett år som ska köra en simpel websida med en enkel databas, alt installerat på samma Linux server i Europa. 
Parametrar för servern: minst 2 CPUs, 8GB RAM och 10 GB disk.


Hosting företag/molnservice | Plan | Pris per månad, US dollars
Molnet Azure|[Virtual machines, Instance B2ms](https://azure.microsoft.com/en-us/pricing/calculator/)|41 (1 year reserved)
Google Cloud |[e2-standard-2](https://cloud.google.com/products/calculator/#id=5f94141e-d3cb-4bda-ad64-173a52b240d8)|63
hosting företag Contabo | [VPS S](https://contabo.com/en/vps/vps-s-ssd/?image=ubuntu.267&qty=1&contract=1&storage-type=vps-s-200-gb-ssd)|6
hosting företag  VPS2day |[VPS Enterprise](https://www.vps2day.com/pricing/)|40


*Källor:*


* [Molnbegrepp – principerna bakom molnbaserad databehandling](https://docs.microsoft.com/sv-se/learn/modules/fundamental-azure-concepts/)
* [​Is the cloud really just someone else's computer?](https://www.techrepublic.com/article/is-the-cloud-really-just-someone-elses-computer/)
* [What is the Cloud? Soft and Fluffy Edition](https://www.youtube.com/watch?v=BO6jvQ88ICQ)


