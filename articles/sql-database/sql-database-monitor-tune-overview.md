---
title: Bewaking en het afstemmen van prestaties - Azure SQL Database | Microsoft Docs
description: Tips voor het afstemmen in Azure SQL Database via evaluatie en verbetering van de prestaties.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/06/2018
ms.openlocfilehash: 7a83bb527a4ccea10e8d9d59d9b5f8e6e75ae106
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857802"
---
# <a name="monitoring-and-performance-tuning"></a>Bewaking en prestatieafstemming

Azure SQL-Database wordt automatisch beheerd en flexibele gegevensservice waar kunt u eenvoudig gebruik te bewaken, toevoegen of verwijderen van resources (CPU, geheugen, i/o-), aanbevelingen die kunnen verbeteren de prestaties van uw database of database aan te passen aan uw workload kunt vinden en automatisch optimaliseren.

## <a name="the-state-of-an-active-query"></a>De status van een actieve query

Azure SQL Database om prestaties te verbeteren, te begrijpen dat elke actieve query-aanvraag vanuit uw toepassing in een die wordt uitgevoerd of in een wachtrij staat. Bij het oplossen van een prestatieprobleem in Azure SQL Database, houd rekening met het volgende diagram:

![Statussen van de werkbelasting](./media/sql-database-monitor-tune-overview/workload-states.png)

Voor een werkbelasting met problemen met prestaties, het uitgeven van de prestaties van mijn worden veroorzaakt door CPU conflicten (een **uitvoeren met betrekking tot** voorwaarde) of afzonderlijke query's zijn er wordt gewacht op iets (een **wachten met betrekking tot** voorwaarde) .

- **Bovenmatig CPU-gebruik in uw Azure SQL database**:

  U ziet mogelijk buitensporig CPU-gebruik waardoor prestatieproblemen in de volgende omstandigheden:

  - Te veel actieve query 's
  - Te veel compileren query 's
  - Een of meer uitvoeren query's met behulp van een suboptimale queryplan

  Als dit het geval is bij uw werkbelasting, wordt het doel is te identificeren en de bijbehorende query's af te stemmen of werk de compute-grootte of servicelaag voor het verhogen van de capaciteit van uw Azure SQL database te begrijpen van de CPU-vereisten. Zie voor meer informatie in het schalen van resources voor individuele databases [schalen van één database-resources in Azure SQL Database](sql-database-single-database-scale.md) en voor het schalen van resources voor elastische pools, Zie [resources voor elastische pool schalen in Azure SQL Database](sql-database-elastic-pool-scale.md).

- **Een afzonderlijke query wacht op iets**

  Afzonderlijke query's mogelijk problemen met prestaties vanwege de query iets wachten. In dit scenario wordt het doel is om te verwijderen of de wachttijd verminderen.

### <a name="determine-if-you-have-a-running-related-performance-issue"></a>Bepalen of u een prestatieprobleem met betrekking tot het uitvoeren van hebt

U kunt uitvoeren met betrekking tot prestatieproblemen met behulp van verschillende methoden kunt identificeren. De meest voorkomende methoden zijn:

- Gebruik de [Azure-portal](#monitor-databases-using-the-azure-portal) voor het bewaken van CPU-percentage gebruik.
- Gebruik de volgende [dynamische beheerweergaven](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) verbruik van CPU, i/o en geheugen voor een Azure SQL Database-database geretourneerd. Er bestaat één rij voor elke 15 seconden, zelfs als er geen activiteit in de database. Historische gegevens worden bijgehouden voor één uur.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) CPU-gebruik en de opslag-gegevens geretourneerd voor een Azure SQL-Database. De gegevens worden verzameld en samengevoegd binnen vijf minuten durende intervallen.

> [!TIP]
> Als een algemene richtlijn als het CPU-gebruik is consistent op of boven 80%, hebt u een probleem met betrekking tot het uitvoeren.

### <a name="determine-if-you-have-a-waiting-related-performance-issue"></a>Bepalen of u een probleem met betrekking tot wachten hebt

Eerst, worden bepaalde dit is niet een probleem met hoge CPU, die wordt uitgevoerd met betrekking tot prestaties. Als dat niet het geval is, wordt de volgende stap is het identificeren van de bovenste wacht die zijn gekoppeld aan de workload van uw toepassing.  Algemene methoden voor het weergeven van de bovenkant wachten categorieën van het type:

- De [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) voorziet in statistieken per query Wacht na verloop van tijd. In Query Store, zijn wacht typen gecombineerd tot wacht categorieën. De toewijzing van de categorieën wait moet worden gewacht typen is beschikbaar in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retourneert informatie over alle wacht geconfronteerd threads die tijdens de bewerking wordt uitgevoerd. Deze geaggregeerde weergave kunt u prestatieproblemen met Azure SQL Database en met specifieke query's en batches.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retourneert informatie over de wachtrij wacht van taken die op een resource wachten.

Zoals u in het vorige diagram, zijn de meest voorkomende wacht:

- Vergrendelingen (blokkeren)
- I/O
- conflicten met betrekking tot tempdb
- Wachten op het verlenen van geheugen

Afhankelijk van wat u ziet, heeft elke categorie wacht een ander pad voor het oplossen van problemen.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Overzicht van de databaseprestaties bewaken in Azure SQL Database

Het bewaken van de prestaties van een SQL-database in Azure begint met het bewaken van het resourcegebruik ten opzichte van het gekozen niveau van databaseprestaties. Bewaking helpt u te bepalen of uw database overtollige capaciteit heeft of problemen heeft juist omdat resources volledig worden benut uit en klikt u vervolgens beslissen of is het tijd om de grootte van compute aanpassen en service-lagen van de database in de [kopen op basis van DTU model](sql-database-service-tiers-dtu.md) of [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md). U kunt uw database bewaken in de [Azure-portal](https://portal.azure.com) met behulp van de volgende grafische hulpprogramma's of SQL [dynamische beheerweergave (DMV's)](sql-database-monitoring-with-dmvs.md).

Azure SQL Database kunt u voor het identificeren van kansen te verbeteren en optimaliseren van prestaties van query's zonder te hoeven wijzigen van resources aan de hand [aanbevelingen voor afstemming](sql-database-advisor.md). Ontbrekende indexen en onvoldoende geoptimaliseerde query's zijn veelvoorkomende redenen voor slechte databaseprestaties. U kunt deze aanbevelingen voor afstemming voor het verbeteren van de prestaties van uw workload kunt toepassen.
U kunt ook laat Azure SQL database tot [automatisch de prestaties van uw query's optimaliseren](sql-database-automatic-tuning.md) door het toepassen van alle aanbevelingen en te controleren dat ze betere databaseprestaties geïdentificeerd. U hebt de volgende opties voor controle en probleemoplossing van de prestaties van de database:

- In de [Azure-portal](https://portal.azure.com), klikt u op **SQL-databases**, selecteert u de database en gebruik vervolgens de controle grafiek om te zoeken naar resources hun maximale nadert. DTU-verbruik wordt standaard weergegeven. Klik op **bewerken** te wijzigen van het tijdsbereik en de waarden die worden weergegeven.
- Gebruik [Query Performance Insight](sql-database-query-performance.md) voor het identificeren van de query's die besteden aan het meest van resources.
- Gebruik [SQL Database Advisor](sql-database-advisor-portal.md) om aanbevelingen voor het maken en verwijderen van indexen, parametriseren query's en schema-problemen oplossen weer te geven.
- Gebruik [Intelligent Insights van Azure SQL](sql-database-intelligent-insights.md) voor de automatische bewaking van de databaseprestaties van uw. Zodra een prestatieprobleem wordt gedetecteerd, wordt een diagnostisch logboek gegenereerd met de details en Root oorzaak Analysis (RCA) van het probleem. Prestaties verbetering aanbeveling wordt geleverd, indien mogelijk.
- [Automatisch instellen inschakelen](sql-database-automatic-tuning-enable.md) en laat Azure SQL database automatisch correctie geïdentificeerd prestatieproblemen kunnen voordoen.
- U kunt ook gebruiken [dynamische beheerweergave (DMV's)](sql-database-monitoring-with-dmvs.md), [uitgebreid gebeurtenissen (`XEvents`) (sql-database/sql-database-xevent-db-diff-from-svr.md), en de [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) om prestaties te verkrijgen de parameters in realtime. Zie [prestatierichtlijnen](sql-database-performance-guidance.md) technieken die u gebruiken kunt om prestaties te verbeteren van Azure SQL Database als u een probleem opgetreden met behulp van deze rapporten of weergaven gevonden.

## <a name="monitor-databases-using-the-azure-portal"></a>Databases bewaken via de Azure-portal

In de [Azure-portal](https://portal.azure.com/) kunt u het verbruik van een individuele database bewaken door de database te selecteren en op de grafiek **Bewaking** te klikken. Nu wordt een venster **Metrische gegevens** geopend, dat u kunt wijzigen door op de knop **Grafiek bewerken** te klikken. Voeg de volgende metrische gegevens toe:

- CPU-percentage
- DTU-percentage
- Gegevens-I/O-percentage
- Databaseomvangpercentage

Als u deze metrische gegevens hebt toegevoegd, kunt u doorgaan met deze bekijken in de **bewaking** grafiek met meer informatie over de **Metric** venster. De vier metrische gegevens tonen het gemiddelde gebruikspercentage ten opzichte van de **DTU** van uw database. Zie de [DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md) en [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md) artikelen voor meer informatie over Servicelagen.  

![Servicelaagbewaking van databaseprestaties.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

U kunt ook meldingen configureren voor prestatiewaarden. Klik op de knop **Melding toevoegen** in het venster **Metrische gegevens**. Volg de wizard om de melding te configureren. U hebt de keuze om een melding weer te geven als de metrische gegevens een bepaalde drempelwaarde overschrijden of als het meetpunt onder een bepaalde drempelwaarde komt.

Als u bijvoorbeeld verwacht dat de workload van de database zal toenemen, kunt u configureren dat er een e-mailmelding wordt verstuurd wanneer de database 80% van een van de prestatiewaarden heeft bereikt. U kunt dit gebruiken als een vroegtijdige waarschuwing te achterhalen wanneer u misschien moet overschakelen naar de volgende hogere compute-grootte.

De maatstaven voor prestaties kunt u bepalen of u moet downgraden naar een lagere compute-grootte. Stel dat u een Standard S2-database gebruikt en alle prestatiewaarden aangeven dat de database gemiddeld nooit meer dan 10% gebruikt. Het is dan waarschijnlijk dat de database in Standard S1 goed werkt. Echter rekening houden met workloads die pieken of fluctueren, voordat u de beslissing om te verplaatsen naar een lagere compute-grootte.

## <a name="improving-database-performance-with-more-resources"></a>Verbetering van de prestaties van de database met andere bronnen

Ten slotte, als er geen bruikbare items die de prestaties van uw database kunnen verbeteren, kunt u de hoeveelheid beschikbare resources in Azure SQL Database. U kunt meer resources toewijzen door het veranderen van de [DTU-servicelaag](sql-database-service-tiers-dtu.md) van een individuele database of een toename van het aantal edtu's van een elastische pool op elk gewenst moment. U kunt ook als u de [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md), kunt u de servicelaag wijzigen of de resources die zijn toegewezen aan uw database te verhogen.

1. Voor individuele databases, kunt u [Servicelagen](sql-database-service-tiers-dtu.md) of [rekenresources](sql-database-service-tiers-vcore.md) on-demand om betere databaseprestaties.
2. Overweeg het gebruik van meerdere databases, [elastische pools](sql-database-elastic-pool-guidance.md) bronnen automatisch schalen.

## <a name="tune-and-refactor-application-or-database-code"></a>Afstemmen en herstructureren toepassing of databasecode

U kunt de code van de toepassing als u wilt meer optimaal gebruik van de database, wijzigen van indexen, geforceerd plannen of hints gebruiken om handmatig de database aan uw workload kunt wijzigen. Vindt u enkele richtlijnen en tips voor handmatige afstemmen en voor het herschrijven van de code in de [prestaties richtlijnen onderwerp](sql-database-performance-guidance.md) artikel.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt inschakelen automatisch afstemmen in Azure SQL Database en laat de functie voor automatisch afstemmen uw werkbelasting volledig te beheren, Zie [automatisch instellen inschakelen](sql-database-automatic-tuning-enable.md).
- Voor het gebruik van handmatige afstemmen, kunt u bekijken [aanbevelingen in Azure-portal voor het afstemmen](sql-database-advisor-portal.md) en handmatig toe te passen degene die de prestaties van uw query's verbeteren.
- Wijzigen van de resources die beschikbaar in uw database door het veranderen van zijn [Azure SQL Database-Servicelagen](sql-database-performance-guidance.md)
