---
title: Overzicht van grootschalige voor Azure SQL Database | Microsoft Docs
description: In dit artikel beschrijft de grootschalige service tier in het op vCore gebaseerde aankoopmodel in Azure SQL Database en wordt uitgelegd wat is het verschil met de Servicelagen voor algemeen gebruik en bedrijfskritiek.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 47a2404361c8ce3f30a0564378857f5a86232a52
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868090"
---
# <a name="hyperscale-service-tier-preview"></a>Zeer grootschalige servicelaag (preview)

De servicelaag grootschalige in Azure SQL Database is de nieuwste service tier in het op vCore gebaseerde aankoopmodel. Deze servicelaag is een zeer schaalbare opslag en compute-prestatielaag die gebruikmaakt van de Azure-architectuur om te schalen om de opslag en rekenresources voor een Azure SQL-Database aanzienlijk buiten de grenzen beschikbaar voor de algemeen gebruik en het bedrijf Kritieke Servicelagen.

> [!IMPORTANT]
> Zeer grootschalige service-laag is momenteel in openbare preview en beschikbaar is in de beperkte Azure-regio's. Zie voor een regiolijst volledig [grootschalige service beschikbare regio's voor een laag](#available-regions). Wordt niet aanbevolen om nog een productieworkload uitvoert in grootschalige databases. U kunt een grootschalige-database niet bijwerken naar een andere service-laag. Voor test-doeleinden aangeraden u een kopie van de huidige database maken en bijwerken van de kopie naar grootschalige servicelaag.
> [!NOTE]
> Zie voor meer informatie over de Servicelagen voor algemeen gebruik en bedrijfskritiek in het op vCore gebaseerde aankoopmodel [Servicelagen voor algemeen gebruik en bedrijfskritiek](sql-database-service-tiers-general-purpose-business-critical.md). Zie voor een vergelijking van de vCore gebaseerde aankoopmodel met het op DTU gebaseerde aankoopmodel [Azure SQL Database-modellen en -bronnen aanschaffen](sql-database-service-tiers.md).
> [!IMPORTANT]
> Zeer grootschalige service-laag is momenteel in openbare preview. Wordt niet aanbevolen om nog een productieworkload uitvoert in grootschalige databases. U kunt een grootschalige-database niet bijwerken naar een andere service-laag. Voor test-doeleinden aangeraden u een kopie van de huidige database maken en bijwerken van de kopie naar grootschalige servicelaag.

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>Wat zijn de mogelijkheden van de servicelaag grootschalige

De servicelaag grootschalige in Azure SQL Database biedt de volgende aanvullende mogelijkheden:

- Ondersteuning voor maximaal een 100 TB aan de grootte van de database
- Nagenoeg onmiddellijke back-ups (op basis van bestand momentopnamen die zijn opgeslagen in Azure Blob-opslag), ongeacht de grootte geen i/o-invloed hebben op Compute-database
- Database terugzetten (op basis van momentopnamen van het bestand) in minuten in plaats van uren of dagen snel (niet een grootte van gegevens)
- Betere algehele prestaties vanwege de hogere doorvoer van het logboek en snellere transactie doorvoeren tijden, ongeacht de gegevensvolumes
- Snelle scale-out - kunt u een of meer alleen-lezen knooppunten voor het offloaden van uw workload voor lezen en voor gebruik als hot-stand-bys inrichten
- Snelle schaal omhoog - u kunt, constante tijdstip opschalen uw rekenresources voor zware werklasten, wanneer nodig, en de compute-resources worden vervolgens weer omlaag schalen wanneer u niet nodig hebt.

De servicelaag grootschalige Hiermee verwijdert u veel van de praktische grenzen traditioneel gezien in clouddatabases. Waar de meeste andere databases zijn beperkt tot door de beschikbare resources in een enkel knooppunt hebben databases in de zeer grootschalige service tier geen dergelijke beperkingen. Met de flexibele opslagarchitectuur groeit opslag naar behoefte. In feite niet worden zeer grootschalige databases gemaakt met een gedefinieerde maximale grootte. De database van een grootschalige neemt toe naarmate er nodig - en worden in rekening gebracht voor de capaciteit die u gebruikt. Voor lees-intensieve werkbelastingen biedt de servicelaag grootschalige snelle scale-out in te richten extra lezen-replica's zo nodig voor offloading Lees-workloads.

Bovendien is de tijd nodig is om te maken van databaseback-ups of om omhoog te schalen of omlaag is niet langer gekoppeld aan het volume van gegevens in de database. Zeer grootschalige databases kunnen worden back-ups bijna onmiddellijk. Ook kunt u een database in de tientallen terabytes omhoog of omlaag schalen binnen enkele minuten. Deze mogelijkheid hoeft u geen zorgen maakt over worden vakken door de initiële configuratie-opties.

Zie voor meer informatie over de compute-grootten voor de servicelaag van grootschalige, [Service tier kenmerken](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wie moet rekening houden met de servicelaag grootschalige

De servicelaag is voornamelijk bedoeld voor klanten met grote databases grootschalige hetzij on-premises en wilt laten hun toepassingen moderniseren door over te stappen naar de cloud of voor klanten die zich al in de cloud en worden beperkt door de maximale databasegrootte beperkingen (1-4 TB). Het is ook bedoeld voor klanten die hoge prestaties en hoge schaalbaarheid voor opslag en compute.

De servicelaag grootschalige biedt ondersteuning voor alle SQL Server-workloads, maar is voornamelijk geoptimaliseerd voor OLTP. De servicelaag grootschalige biedt ook ondersteuning voor hybride en analytische workloads (datamart).

> [!IMPORTANT]
> Elastische pools bieden geen ondersteuning voor de grootschalige service tier.

## <a name="understand-hyperscale-pricing"></a>Flexibele prijzen

Zeer grootschalige servicelaag is alleen beschikbaar in [vCore-model](sql-database-service-tiers-vcore.md). Zodat deze overeenkomt met de nieuwe architectuur, is het prijsmodel enigszins afwijken van de service-lagen voor algemeen gebruik en bedrijfskritiek:

- **COMPUTE**:

  De prijs van grootschalige compute-eenheid is per replica. De [Azure hybride Benifit](https://azure.microsoft.com/pricing/hybrid-benefit/) prijs als u wilt lezen schaal replica's automatisch wordt toegepast. In openbare preview-versie maken we twee replica's per database zeer grootschalige standaard.

- **Opslag**:

  U hoeft niet te geven van de maximale gegevensgrootte bij het configureren van een grootschalige-database. In de laag Hyperscale wordt opslag voor uw database in rekening gebracht op basis van daadwerkelijk gebruik. Opslag wordt dynamisch toegewezen tussen 5 GB en 100 TB, in stappen van 1 GB.  

Zie voor meer informatie over de prijzen voor grootschalige [prijzen van Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Architectuur: Distributie van functies voor het isoleren van mogelijkheden

In tegenstelling tot traditionele database-engines die beschikken over alle van de functies voor het beheer van gegevens in één locatie/proces gecentraliseerde (zelfs zodat aangeroepen gedistribueerde databases in de productieomgeving vandaag meerdere exemplaren van een monolithisch gegevensengine hebben), een database zeer grootschalige worden gescheiden de verwerking van query's-engine, waar de semantiek van verschillende gegevensengines afwijkt van de onderdelen die langdurige opslag en duurzaamheid voor de gegevens bieden. Op deze manier kunnen de opslagcapaciteit kan worden soepel uitgebreid zo ver nodig (eerste doel is 100 TB). Alleen-lezen replica's delen dezelfde compute-onderdelen, zodat geen kopiëren van gegevens is vereist voor het instellen van een nieuwe leesbare replica.

Het volgende diagram illustreert de verschillende soorten knooppunten in een grote database:

![architectuur](./media/sql-database-hyperscale/hyperscale-architecture.png)

Een grote database bevat de volgende soorten knooppunten:

### <a name="compute-node"></a>Rekenknooppunt

De compute-knooppunt is waar de relationele engine zich bevinden, zodat alle de elementen van de taal, verwerking van query's, enzovoort, optreden. Alle interactie van gebruikers met een grote database gebeurt via deze compute-knooppunten. COMPUTE-knooppunten op basis van SSD-caches (met de naam RBPEX - Tolerante Buffergroepuitbreiding in het voorgaande diagram) hebben om te beperken het nummer van het netwerk retouren zijn vereist voor het ophalen van een pagina met gegevens. Er is een primaire rekenknooppunt waar de werkbelastingen voor lezen / schrijven en transacties worden verwerkt. Er zijn een of meer secundaire compute-knooppunten die fungeren als hot stand-by-knooppunten voor failover-doeleinden, evenals fungeren als alleen-lezen rekenknooppunten voor het offloading-workloads (als deze functionaliteit is vereist) te lezen.

### <a name="page-server-node"></a>Pagina server-knooppunt

Pagina-servers zijn systemen voor een scale-out opslag-engine.  Elke pagina-server is verantwoordelijk voor een subset van de pagina's in de database.  Elke pagina server bepaalt nominaal, 1 terabyte aan gegevens. Er zijn geen gegevens worden gedeeld op meer dan één pagina server (buiten de replica's die worden bijgehouden voor redundantie en beschikbaarheid). De taak van een pagina-server is voor het bieden van database-pagina's uit naar de rekenknooppunten op aanvraag, en het behouden van de pagina's bijgewerkt als transacties gegevens bijwerken. Pagina-servers worden up-to-date gehouden door af te spelen records in logboek registreren vanuit de log-service. Pagina servers onderhouden ook caches op basis van SSD voor betere prestaties. Langdurige opslag van gegevenspagina's wordt opgeslagen in Azure Storage voor extra betrouwbaarheid.

### <a name="log-service-node"></a>Logboek-knooppunt

Het knooppunt van de service log records in logboek registreren van het primaire rekenknooppunt accepteert, ze zich blijft voordoen in een duurzame cache en verzendt de records in logboek registreren voor de rest van de compute-knooppunten (zodat ze hun caches bijwerken kunnen) en de relevante pagina (s), zodat de gegevens bijgewerkt t worden kunnen Hier volgen enkele. Op deze manier worden alle gegevenswijzigingen van de primaire rekenknooppunt doorgegeven via de logboekservice tot alle secundaire compute-knooppunten en pagina-servers. Ten slotte worden het log-record (s) doorgegeven aan opslag in Azure Storage, dit een opslagplaats voor onbeperkte opslag is op lange termijn. Dit mechanisme Hiermee verwijdert u de noodzaak van regelmatig moet worden afgekapt. De log-service heeft ook een lokale cache om toegang te versnellen.

### <a name="azure-storage-node"></a>Azure storage-knooppunt

Het Azure storage-knooppunt is de uiteindelijke bestemming van gegevens van pagina-servers. Deze opslag wordt gebruikt voor back-updoeleinden ook als die voor replicatie tussen Azure-regio's. Back-ups bestaan uit momentopnamen van bestanden. Herstellen bewerking zijn snel uit deze momentopnamen en gegevens kunnen worden hersteld naar een bepaald tijdstip.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Back-ups base momentopname van de bestanden en ze zijn daarom nagenoeg onmiddellijke. Opslag- en rekencapaciteit gescheiden inschakelen in de back-up-/ herstelbewerking pushen naar de storage-laag te verminderen van de werkbelasting van de verwerking van het primaire knooppunt. Als gevolg hiervan is de back-up van een grote database niet van invloed op de prestaties van het primaire knooppunt. Op deze manier herstelacties moeten worden uitgevoerd door de momentopname van het bestand kopiëren en als zodanig zijn niet een grootte van gegevens. De herstelbewerking is voor herstelbewerkingen binnen hetzelfde opslagaccount, snel.

## <a name="scale-and-performance-advantages"></a>Schaal en prestaties voordelen

Met de mogelijkheid om snel automatisch instellen/uitschakelen als u meer alleen-lezen-compute-knooppunten, de architectuur kan aanzienlijke grootschalige mogelijkheden voor lezen en kan ook het primaire knooppunt voor het uitvoeren van meer schrijfaanvragen vrij. Ook kunnen de compute-knooppunten worden geschaald omhoog/omlaag snel vanwege de gedeelde opslag-architectuur van de grootschalige-architectuur.

## <a name="available-regions"></a>Beschikbare regio's

Zeer grootschalige service-laag is momenteel in openbare preview en beschikbaar zijn in de volgende Azure-regio's: EastUS1, EastUS2, WestUS2, CentralUS, NorthCentralUS, Europa West, NorthEurope, UKWest, AustraliaEast, AustraliaSouthEast, SouthEastAsia, JapanEast, KoreaCentral

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over Servicelagen [Servicelagen](sql-database-service-tiers.md)
- Zie [overzicht van resource beperkt op een logische server](sql-database-resource-limits-logical-server.md) voor informatie over de beperkingen op het niveau van de server en -abonnement.
- Zie voor het aanschaffen van model-limieten voor één database [Azure SQL Database vCore gebaseerde model limieten voor één database aanschaffen](sql-database-vcore-resource-limits-single-databases.md).
- Voor een functies en van de vergelijkingslijst, Zie [algemene SQL-functies](sql-database-features.md).
