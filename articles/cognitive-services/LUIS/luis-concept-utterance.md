---
title: Uitingen in LUIS-apps
titleSuffix: Azure Cognitive Services
description: Uitingen zijn invoer van de gebruiker die uw app moet worden geïnterpreteerd. Items waarvan u denkt dat gebruikers verzamelen. Uitingen die dezelfde betekenis, maar zijn samengesteld anders opnemen in word lengte en de plaatsing van word.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 39c99cc35f4c2549efc9c20af0680b77483325c5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038932"
---
# <a name="utterances-in-luis"></a>Uitingen in LUIS

**Uitingen** zijn invoer van de gebruiker die uw app moet worden geïnterpreteerd. LUIS intenties en entiteiten ophalen uit deze trainen, is het belangrijk om vast te leggen van tal van verschillende soorten invoer voor elk doel. Actief leren, of het proces voor u doorgaat met het trainen op nieuwe uitingen is essentieel voor hebt geleerd van machine-intelligentie die LUIS biedt.

Items waarvan u denkt dat gebruikers verzamelen. Uitingen die dezelfde betekenis, maar zijn samengesteld anders opnemen in word lengte en de plaatsing van word. 

## <a name="how-to-choose-varied-utterances"></a>Uiteenlopende uitingen kiezen
Wanneer u eerst aan de slag door [toe te voegen voorbeeld uitingen](luis-how-to-add-example-utterances.md) aan uw LUIS-model, worden hier enkele principes rekening moet houden.

### <a name="utterances-arent-always-well-formed"></a>Uitingen zijn niet altijd goed gevormd.
Het is mogelijk een zin, zoals "Book me een ticket naar Parijs", of een fragment van een zin, zoals 'Reservering' of "Parijs vlucht."  Gebruikers maken vaak spelfouten. Overweeg of u spellingcontrole invoer van de gebruiker voordat deze aan LUIS doorgegeven bij het plannen van uw app. De [Bing Spell Check-API] [ BingSpellCheck] kan worden geïntegreerd met LUIS. U kunt uw LUIS-app koppelen aan een externe sleutel voor de Bing Spell Check-API wanneer u deze publiceert. Als het selectievakje gebruiker uitingen niet is gespeld, moet u LUIS trainen op uitingen die typefouten en spelfouten bevatten.

### <a name="use-the-representative-language-of-the-user"></a>Gebruik de representatieve taal van de gebruiker
Bij het kiezen van uitingen er rekening mee dat wat u denkt dat een algemene term of woordgroep mogelijk niet op de gebruikelijke gebruiker van de clienttoepassing. Ze hebben mogelijk niet de domein-ervaring. Dus wees voorzichtig bij het gebruik van termen of zinnen dat een gebruiker alleen zou bijvoorbeeld als ze een expert zijn.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Kies uiteenlopende terminologie, evenals formulering
U vindt dat zelfs als u inspanningen voor het maken van uiteenlopende zin patronen, u nog steeds herhaald sommige vocabulaire.

Deze voorbeeld-uitingen nemen:

|Voorbeelden van utterances|
|--|
|hoe krijg ik een computer?|
|Waar vind ik een computer?|
|Ik wil ophalen van een computer, hoe gaat het erover?|
|Wanneer kan ik een computer hebben?| 

De term core hier is 'computer', niet gewijzigd. Ze konden zeggen desktopcomputer, laptop, werkstation of zelfs alleen machine. LUIS afleidt op intelligente wijze synoniemen uit de context, maar wanneer u uitingen voor training maakt, is het nog steeds beter ze variëren.

## <a name="example-utterances-in-each-intent"></a>Voorbeeld-uitingen in elk doel
Elk doel moet voorbeeld uitingen, ten minste 10 tot 15. Als u een doel dat geen alle uitingen voorbeeld hebt, wordt het niet mogelijk met het trainen van LUIS. Hebt u een doel met één of enkele voorbeeld-uitingen, LUIS wordt niet nauwkeurig te voorspellen de bedoeling. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Kleine groepen van 10-15-uitingen voor elke herhaling authoring toevoegen
In elke herhaling van het model, Voeg een grote hoeveelheid uitingen niet. Utterances toevoegen in hoeveelheden van tientallen. [Train](luis-how-to-train.md), [publiceren](luis-how-to-publish-app.md), en [testen](luis-interactive-test.md) opnieuw.  

LUIS bouwt effectieve modellen met uitingen die goed zijn geselecteerd. Te veel uitingen toevoegen is geen waardevolle omdat het verwarring leidt.  

Het is beter om te beginnen met een paar uitingen vervolgens [eindpunt uitingen bekijken](luis-how-to-review-endoint-utt.md) voor het juiste intentie voorspelling en entiteit ophalen.

## <a name="ignoring-words-and-punctuation"></a>Woorden en leestekens worden genegeerd
Als u wilt dat specifieke woorden of de interpunctieteken in het voorbeeld utterance negeren, gebruikt u een [patroon](luis-concept-patterns.md#pattern-syntax) met de _negeren_ syntaxis. 

## <a name="training-utterances"></a>Training-uitingen
Training is niet-deterministisch: de voorspelling utterance kan enigszins verschillen voor verschillende versies of apps.

## <a name="testing-utterances"></a>Uitingen testen 

Ontwikkelaars moeten beginnen met het testen van hun LUIS-toepassing met echte verkeer door te sturen uitingen naar het eindpunt. Deze uitingen worden gebruikt voor het verbeteren van de prestaties van de intenties en entiteiten met [bekijken uitingen](luis-how-to-review-endoint-utt.md). Tests verzonden met het testen van deelvenster LUIS-website niet via het eindpunt zijn verzonden, en dus niet bijdragen tot actief leren. 

## <a name="review-utterances"></a>Uitingen bekijken
Nadat uw model getraind, gepubliceerde en ontvangende is [eindpunt](luis-glossary.md#endpoint) query's, [bekijken van de uitingen](luis-how-to-review-endoint-utt.md) LUIS worden voorgesteld. LUIS selecteert endpoint-uitingen waarvoor lage scores voor de doel- of entiteit. 

## <a name="best-practices"></a>Aanbevolen procedures
Beoordeling [aanbevolen procedures](luis-concept-best-practices.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [voorbeeld utterances toevoegen](luis-how-to-add-example-utterances.md) voor meer informatie over het trainen van een LUIS-app om te begrijpen uitingen van de gebruiker.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text