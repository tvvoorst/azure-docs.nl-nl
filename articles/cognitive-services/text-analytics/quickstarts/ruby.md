---
title: 'Snelstartgids: Ruby gebruiken om aan te roepen van de Tekstanalyse-API | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Get-informatie en codevoorbeelden om u te helpen snel aan de slag met behulp van de Tekstanalyse-API in Microsoft Cognitive Services op Azure.
services: cognitive-services
documentationcenter: ''
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 1a1e3beb496048104f2635e1eff6a7aaefbfbb30
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296309"
---
# <a name="quickstart-using-ruby-to-call-the-text-analytics-cognitive-service"></a>Snelstartgids: Ruby gebruiken om aan te roepen de Text Analytics-Cognitive Service
<a name="HOLTop"></a>

In dit artikel ziet u hoe u aan [taal detecteren](#Detect), [stemming analyseren](#SentimentAnalysis), [Extraheer sleuteluitdrukkingen](#KeyPhraseExtraction), en [gekoppelde entiteiten identificeren](#Entities) gebruiken de [Tekstanalyse-API's](//go.microsoft.com/fwlink/?LinkID=759711) met Ruby.

Raadpleeg de [API-definities](//go.microsoft.com/fwlink/?LinkID=759346) voor technische documentatie voor de API's.

## <a name="prerequisites"></a>Vereiste onderdelen

Hebt u een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met **Tekstanalyse-API**. U kunt de **gratis laag voor 5000 transacties per maand** om uit te voeren van deze Quick Start.

Ook moet u de [eindpunt en de toegangssleutel](../How-tos/text-analytics-how-to-access-key.md) die is gegenereerd voor u tijdens het aanmelden van. 

<a name="Detect"></a>

## <a name="detect-language"></a>Taal detecteren

De API voor taal detecteert de taal van een document, met behulp van de [taal detecteren methode](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Maak een nieuwe Ruby-project in uw favoriete IDE.
2. Voeg de code hieronder.
3. Vervang de `accessKey` waarde met een geldige toegangssleutel voor uw abonnement.
4. Vervang de locatie in `uri` (momenteel `westus`) naar de regio die u zich heeft aangemeld.
5. Voer het programma.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = 'ENTER KEY HERE'

# Replace or verify the region.
#
# You must use the same region in your REST API call as you used to obtain your access keys.
# For example, if you obtained your access keys from the westus region, replace 
# "westcentralus" in the URI below with "westus".
#
# NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
# a free trial access key, you should not need to change this region.
uri = 'https://westus.api.cognitive.microsoft.com'
path = '/text/analytics/v2.0/'

uri = URI(uri + path + 'languages')

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Taal detecteren-antwoord**

Een geslaagde respons wordt geretourneerd in JSON, zoals wordt weergegeven in het volgende voorbeeld: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Stemming analyseren

De analyse-Gevoels-API-detexts het gevoel van een set tekstrecords, met behulp van de [Sentiment methode](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Het volgende voorbeeld beoordeelt twee documenten, één in het Engels en een andere in het Spaans.

Voeg de volgende code in de code van de [vorige sectie](#Detect).

```ruby
uri = URI(uri + path + 'sentiment')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Sentiment-analyse antwoord**

Een geslaagde respons wordt geretourneerd in JSON, zoals wordt weergegeven in het volgende voorbeeld: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Sleuteltermen ophalen

De API-sleutel woordgroep extractie sleuteltermen geëxtraheerd uit een document, met behulp van de [sleuteltermen methode](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Het volgende voorbeeld haalt sleuteltermen voor Engelse en Spaanse documenten.

Voeg de volgende code in de code van de [vorige sectie](#SentimentAnalysis).

```ruby
uri = URI(uri + path + 'keyPhrases')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Sleuteluitdrukkingen extraheren antwoord**

Een geslaagde respons wordt geretourneerd in JSON, zoals wordt weergegeven in het volgende voorbeeld: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Gekoppelde entiteiten identificeren

De Entity Linking API identificeert bekende entiteiten in een document, met behulp van de [entiteiten koppelen methode](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Het volgende voorbeeld identificeert entiteiten voor Engels-documenten.

Voeg de volgende code in de code van de [vorige sectie](#KeyPhraseExtraction).

```ruby
uri = URI(uri + path + 'entities')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'en', 'text' => 'The Seattle Seahawks won the Super Bowl in 2014.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Entiteit koppelen antwoord**

Een geslaagde respons wordt geretourneerd in JSON, zoals wordt weergegeven in het volgende voorbeeld: 

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zie ook 

 [Text Analytics-overzicht](../overview.md)  
 [Veelgestelde vragen](../text-analytics-resource-faq.md)
