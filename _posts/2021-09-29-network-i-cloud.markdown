---
layout: post
title:  "Nätverk i molnet"
date:   2021-09-29 20:04:59 +0200
author: SvetlanaErn
---


Det finns ett företag som har en intern applikation. Denna interna applikation använder tillgång till interna server och resurser, och jobbar med klassificerade data som inte får öppet skickas via nätat.


Applikationen kräver modernisering bl.a. implementation av en enterprise bus.


Kan man använda för detta Azure Service Bus (samt andra moln tjänster) och samtidigt inte skicka data öppet genom internet?


Två utmaningar uppstår när man försöker lösa detta problem. 
1. Connection mellan företags interna delar (app och resurser) och moln delen (den blåa linjen på bilden). Det är inte säker connection och man få inte skicka känsliga data här. 
2. När två moln tjänster kommunicerar med varandra kan kommunikation hända genom public internet (dem röda linjerna på bilden). Det är inte säker connection heller.   


<img src="/Post_7_1.jpg"/>


Det finns en lösning för varje punkt.


1. Connection mellan företagets nät och moln delen. Man behöver bygga a site-to-site virtual private network (VPN)- en krypterad kanal (förbindelse) mellan Azure och On-Premise miljöer.   
2. Connection mellan två tjänster (eller app) i molnet. Man kan använda Virtual Private Cloud (VPC), som är en del av public moln och dit har tillgång bara vissa användare. Där i VPC skapar man ett virtuellt privat nät (VNET). För att koppla en molntjänst till det virtuella privata nätet sätter på man en endpoint på varje molntjänst. Applikationerna i så fall kopplas till samma VNET via VNet integration eller endpoint. Sådan arkitektur garanterar att kommutation mellan nätdeltagare händer genom privat nät och inte använder public internet. Tillgång till app och molntjänster genom privat endpoints heter Azure Private Link.


<img src="/Post_7_2.jpg"/>


*Källor:*


* [What is Azure Private Endpoint?](https://docs.microsoft.com/da-dk/azure/private-link/private-endpoint-overview)
* [What is Azure Private Link?](https://docs.microsoft.com/da-dk/azure/private-link/private-link-overview)
* [What is Azure Virtual Network?](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)
* [Connect an on-premises network to a Microsoft Azure virtual network](https://docs.microsoft.com/en-us/microsoft-365/enterprise/connect-an-on-premises-network-to-a-microsoft-azure-virtual-network?view=o365-worldwide)




