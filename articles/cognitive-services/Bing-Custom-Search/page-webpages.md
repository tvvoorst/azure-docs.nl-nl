---
title: Bladeren door beschikbare webpagina's - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Laat zien hoe u een pagina via alle van de webpagina's met Bing aangepaste zoekopdrachten kunt terugkeren.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f7d0a2f9c5a0e272d9f46ab76360ea7bf91449cc
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815951"
---
# <a name="paging-webpages"></a>Wisselbestand-webpagina 's 

Wanneer u de Custom Search-API aanroept, retourneert Bing een lijst met resultaten. De lijst is een subset van het totale aantal resultaten die mogelijk relevant zijn voor de query. Als u de geschatte totale aantal beschikbare resultaten, toegang krijgen tot de antwoord-object [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) veld.  
  
Het volgende voorbeeld wordt de `totalEstimatedMatches` veld met een Web-antwoord.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Pagina via de webpagina's die beschikbaar is, gebruikt u de [aantal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) en [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) queryparameters.  
  
De `count` parameter geeft u het aantal resultaten dat moet worden geretourneerd in het antwoord. Het maximum aantal resultaten die u in het antwoord aanvragen kunt is 50. De standaardwaarde is 10. Het werkelijke getal geleverd mogelijk lager dan aangevraagd.

De `offset` parameter geeft u het aantal resultaten dat moet worden overgeslagen. De `offset` is gebaseerd op nul en moet kleiner dan (`totalEstimatedMatches` - `count`).  
  
Als u weergeven van 15 webpagina's per pagina wilt, stelt u `count` tot en met 15 en `offset` op 0 om de eerste pagina van de resultaten. Voor elke volgende pagina opgehoogd `offset` 15 (bijvoorbeeld: 15, 30).  
  
Hieronder ziet u een voorbeeld waarin 15 webpagina's die beginnen bij verschuiving 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Als de standaard `count` waarde werkt voor uw implementatie, u hoeft alleen te geven de `offset` queryparameter.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

