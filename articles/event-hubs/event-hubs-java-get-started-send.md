---
title: Gebeurtenissen verzenden naar Azure Event Hubs met behulp van Java | Microsoft Docs
description: Aan de slag verzenden naar Event Hubs met behulp van Java
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/27/2018
ms.author: shvija
ms.openlocfilehash: f67982eda60a8fdfdf0d50785827c513275fd202
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124752"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Gebeurtenissen verzenden naar Azure Event Hubs met behulp van Java

Eventhubs is een zeer schaalbare opname-systeem dat kan miljoenen gebeurtenissen per seconde opnemen, zodat een toepassing te verwerken en analyseren van de enorme hoeveelheden gegevens die worden geproduceerd door uw verbonden apparaten en toepassingen. Zodra de verzameld in een event hub, kunt u deze kunt transformeren en opslaan van gegevens met behulp van een realtime analytics-provider of opslagcluster.

Zie voor meer informatie de [overzicht van Event Hubs][Event Hubs overview].

Deze zelfstudie laat zien hoe u gebeurtenissen naar een event hub verzendt met behulp van een consoletoepassing in Java. Zie voor het ontvangen van gebeurtenissen met behulp van de Java Event Processor Host-bibliotheek, [in dit artikel](event-hubs-java-get-started-receive-eph.md), of klik op de juiste taal voor ontvangst in de linker inhoudsopgave.

## <a name="prerequisites"></a>Vereisten

Als u wilt in deze zelfstudie hebt voltooid, moet u de volgende vereisten:

* Een Java-ontwikkelomgeving. Deze zelfstudie wordt gebruikgemaakt van [Eclipse](https://www.eclipse.org/).
* Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][] aan voordat u begint.

De code in deze zelfstudie is gebaseerd op de [SimpleSend GitHub voorbeeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), die u kunt controleren om te zien van de volledige werkende toepassing.

## <a name="send-events-to-event-hubs"></a>Gebeurtenissen verzenden naar Event Hubs

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten uit de [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). U kunt verwijzen naar deze bibliotheek met behulp van de volgende afhankelijkheidsverklaring binnen het Maven-projectbestand. De huidige versie is 1.0.2:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Voor verschillende soorten build-omgevingen, kunt u de meest recente vrijgegeven JAR-bestanden van expliciet verkrijgen de [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Voor een eenvoudige gebeurtenisuitgever, importeert u de *com.microsoft.azure.eventhubs* -pakket voor de Event Hubs-clientklassen en de *com.microsoft.azure.servicebus* pakket voor hulpprogramma voor klassen, zoals Algemene uitzonderingen die worden gedeeld met de Azure Service Bus messaging-client. 

### <a name="declare-the-send-class"></a>De klasse verzenden declareren

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Naam van de klasse `SimpleSend`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Verbindingsreeks

De klasse ConnectionStringBuilder gebruiken om samen te stellen van een string-waarde van de verbinding moet worden doorgegeven aan het exemplaar van de client Event Hubs. Vervang de tijdelijke aanduidingen door de waarden die u hebt verkregen tijdens het maken van de naamruimte en event hub:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Gebeurtenissen verzenden

Een enkelvoudige gebeurtenis maken door het omzetten van een tekenreeks in de codering UTF-8 bytes. Vervolgens maakt u een nieuw exemplaar van de Event Hubs-client uit de verbindingsreeks en verzenden van het bericht:   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

### <a name="how-messages-are-routed-to-eventhub-partitions"></a>Hoe berichten worden gerouteerd naar Event hub-partities

Voordat u berichten worden opgehaald door de consumenten, die ze moeten worden gepubliceerd naar de partities eerst door de uitgevers. Wanneer berichten worden gepubliceerd naar event hub synchroon met de methode sendSync() op het object com.microsoft.azure.eventhubs.EventHubClient, kan het bericht worden verzonden naar een specifieke partitie of gedistribueerd naar alle beschikbare partities op een wijze round robin afhankelijk van of de partitiesleutel is opgegeven of niet.

Als een tekenreeks voor de partitiesleutel is opgegeven, wordt de sleutel wordt gehasht om te bepalen welke partitie voor het verzenden van de gebeurtenis.

Als de partitiesleutel is niet ingesteld, klikt u vervolgens wordt berichten round-robined op alle beschikbare partities

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Gebeurtenissen ontvangen met de EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs-overzicht][Event Hubs overview]
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

