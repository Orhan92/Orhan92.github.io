---
layout: post
title: "Networks in the Cloud"
subtitle: "Lecture 7 - Networks in the Cloud (Deadline 2021/09/29 23:55)"
date: 2021-09-29 13:27:18 +0200
categories: jekyll update
background: "/images/image1.jpg"
---

# Introduction

In this blog post we are going to persuade our CTO to implement our work into Azure. Our company CTO doesn't want our intern data to be exposed to the public. Therefore he is a little bit sceptical to use Azure cloud solutions. My job is to convince by CTO to work with Azure and how we still can maintain our data private even though we are using Azure Cloud Services. I will explain how we can use a Virtual Private Cloud solution within Azure (VPC). Because our CTO is a swedish speaking person, I will have to send an email (in swedish) to him in order to persuade him to use Azure.

## Email to CTO

"_Hej, CTO_

_Jag tycker att vi ska börja använda oss av Azure. Anledningen till detta är pga att vi skulle kunna och använda fler utav Azures tjänster samt att vi kan ha all vår data samlad på en och samma plats. Dessutom får vi möjligheten till att skapa infrastrukturen för att köra allt via molnet. Hur ska detta fungera undrar du nu eftersom att vi arbetar med klassificerad data? Azure erbjuder en tjänst som heter ’Azure Private Link’. Med hjälp av detta så ger det oss en möjlighet att skapa en privat uppkoppling mellan våra interna servrar och Azure. Vi kan därmed utnyttja andra tjänster hos Azure för att på säkert sätt skicka data mellan Azure och våra interna servrar. Ett sådant exempel kan vara att använda Azure Service Bus. I och med att vi använder oss utav Azure Private Link så kan vi vara säkra på att data skickas över en privat uppkoppling istället för att exponeras publikt._

_Så det vi kommer behöva göra är att skapa infrastrukturen för vårat privata virtuella nätverk i Azure. För att säkert behandla vår data inom organisationen kommer vi behöva att sätta upp ett ’Virtuellt nätverk’ som innehåller en ’Private Endpoint’. Därefter kommer vi att behöva använda tjänsten ’Azure Private Link’ för att skapa en privat uppkoppling från oss till Azure molnet. Därefter kommer vi kunna använda ’Service Bus’ för att säkert och privat skicka data efter att vi satt upp vårt privata virtuella nätverk. Se nedan där jag kommer att förklara dessa olika begrepp som jag nämnt här ovan:_

**Virtuellt Nätverk (VNet)**: _Skapa ett virtuellt nätverk. Ett virtuellt nätverk är en representation av vårat egna nätverk i molnet. Vi kommer att kunna konfiguera vårat virtuella nätverk att kommunicera direkt och säkert med vår virtuella maskin. Detta gör att vi kan skicka data mellan våra interna servrar och molnet på en privat uppkoppling och inte en publik (precis det du är ute efter CTO!), dessutom med Azure Service Bus, bra va??_

**Private Endpoint**: _Nästa steg hade varit att sätta upp en ’Private Endpoint’ inuti vår virtuella nätverk. Detta är ett nätverks ’interface’ som använder sig av en privat IP adress från vårat virtuella nätverk. Vår ’Private Endpoint’ kommer då att koppla upp oss privat och säkert med molnet genom att använda en tjänst som heter ’Azure Private Link’._

**Azure Private Link**: _Denna tjänst tillåter oss att få åtkomst till Azure tjänster som SQL Databaser, Azure Storages osv, genom en privat uppkoppling. På så vis behöver vi inte exponera våra tjänster eller data publikt och kan istället hålla/skicka samtlig data privat mellan oss och molnet._

**Service Bus**: _En service bus möjliggör att vi kan skicka data på och garantera att data kommer fram med hjälp av exempelvis ’Queue Messaging’. Vi kan med hjälp av denna tjänst se till att data inte går förlorad, inte dupliceras och att det behandlas i tur och ordning med exempelvis FIFO (First-in-First-out). Denna tjänst kan vi använda oss utav nu när vi skapat upp vårat privata nätverk för att koppla upp (och skicka data) från våra interna serverar (On-premises) till molnet. Vi kan även konfiguera vår bus och säkerställa att det vi skickar garanteras att skickas ”At least once delivery of a message” (åtminstone en gång)._

_Allt ovan tillsammans gör att vi använder oss av en virtual private cloud (VPC). Vi isolerar vår molnlösning till ett privat nätverk med hjälp av Azure Private Link som hostas av en public cloud (Azure). Genom att samla allt, privat, hos Azure kan vi därefter utnyttja massvis med andra bra tjänster hos Azure som kan stå oss till tjänst. I och med att vi använder oss av en VPC så innebär det också att data inte skickas publikt via internet utan istället skickas över en privat uppkoppling mellan våra interna servar (On-premises) och Azure. Så det vi gör egentligen är att vi kör vår Private Link mellan vårat virtuella nätverk och Azure Services för att få åtkomst till Azure’s tjänster på ett privat nätverk. Så hur fungerar det rent visuellt? Se illustration här nedan:_"

![Virtual Private Network](/images/virtual-private-network.png){:class="img-fluid"}

#### References

[Private Endpoint](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview)\
[Service Bus](https://docs.microsoft.com/en-us/azure/event-grid/compare-messaging-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fservice-bus-messaging%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)\
[Service Bus Messagin](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)\
[Private Link](https://docs.microsoft.com/en-us/azure/private-link/private-link-overview)\
[Virtual Networks](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-faq)
