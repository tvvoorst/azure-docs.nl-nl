---
title: Azure SQL Database - query's op de replica's lezen | Microsoft Docs
description: De Azure SQL Database biedt de mogelijkheid om te laden saldo-werkbelastingen alleen-lezen met behulp van de capaciteit van alleen-lezen replica's - Read Scale-Out genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/01/2018
ms.openlocfilehash: bc322857a459f9417ed7c89a6e4df7ce5c41c3f0
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246478"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Alleen-lezen replica's gebruiken om te laden saldo alleen-lezen query workloads (preview)

**Read Scale-Out** kunt u saldo Azure SQL Database alleen-lezen-werkbelastingen met behulp van de capaciteit van een alleen-lezen-replica. 

## <a name="overview-of-read-scale-out"></a>Overzicht van Scale-Out van lezen

Elke database in de Premium-laag ([DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md)) of in de laag bedrijfskritiek ([vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md)) automatisch is ingericht met verschillende AlwaysON-replica's op ondersteuning voor de beschikbaarheids-SLA.

![Alleen-lezen replica 's](media/sql-database-managed-instance/business-critical-service-tier.png)

Deze replica's worden ingericht met de dezelfde compute-grootte als de alleen-lezen-replica die worden gebruikt door de normale databaseverbindingen. De **Read Scale-Out** functie kunt u saldo SQL-Database alleen-lezen-werkbelastingen met behulp van de capaciteit van een van de alleen-lezen replica's in plaats van het delen van de replica voor lezen / schrijven. Op deze manier de alleen-lezen-werkbelasting worden geïsoleerd van de belangrijkste workload voor lezen / schrijven en heeft geen invloed op de prestaties. De functie is bedoeld voor de toepassingen die logisch zijn gescheiden van de alleen-lezen werkbelastingen, zoals analytics, en daarom kunnen krijgen prestatievoordelen met behulp van deze extra capaciteit zonder extra kosten.

Voor het gebruik van de functie Read Scale-Out met een bepaalde database, moet u expliciet inschakelen dit bij het maken van de database of later door het wijzigen van de configuratie met behulp van PowerShell door het aanroepen van de [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) of de [ Nieuwe-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlets of via de REST-API van Azure Resource Manager met behulp de [Databases - maken of bijwerken](/rest/api/sql/databases/createorupdate) methode. 

Nadat Read Scale-Out is ingeschakeld voor een database, toepassingen die verbinding maken met deze database worden omgeleid naar de alleen-lezen-replica of naar een alleen-lezen replica van die database volgens de `ApplicationIntent` eigenschap geconfigureerd in van de toepassing de verbindingsreeks. Voor meer informatie over de `ApplicationIntent` eigenschap, Zie [Toepassingsintentie op te geven](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Als Read Scale-Out is uitgeschakeld of als u de eigenschap ReadScale in een niet-ondersteunde servicelaag instellen, alle verbindingen worden doorgestuurd naar de alleen-lezen replica, onafhankelijk van de `ApplicationIntent` eigenschap.

> [!NOTE]
> Tijdens de Preview-versie, worden Query Data Store en Extended Events niet ondersteund op de alleen-lezen replica's.

## <a name="data-consistency"></a>Gegevensconsistentie

Een van de voordelen van het Always ON is dat de replica's altijd de transactioneel consistente status hebben, maar op verschillende momenten in de tijd kunnen er enkele kleine latentie tussen verschillende replica's. Read Scale-Out biedt ondersteuning voor consistentie van de sessie-niveau. Het betekent als de alleen-lezen-sessie opnieuw verbinding maakt nadat een verbindingsfout veroorzaakt door replica niet beschikbaar zijn, kan deze worden omgeleid naar een replica die is niet 100% up-to-date met de replica voor lezen / schrijven. Op dezelfde manier als een toepassing schrijft gegevens met behulp van een sessie voor lezen / schrijven en leest direct met behulp van een alleen-lezen-sessie, is het mogelijk dat de meest recente updates niet meteen zichtbaar zijn. Dit is omdat de transactie log opnieuw op de replica's asynchroon is.

> [!NOTE]
> Replicatielatentie binnen de regio laag zijn en deze situatie is zeldzaam.


## <a name="connecting-to-a-read-only-replica"></a>Verbinding maken met een alleen-lezen replica

Wanneer u Read Scale-Out voor een database inschakelen, de `ApplicationIntent` optie in de verbindingsreeks die is opgegeven door de client bepaalt of de verbinding wordt doorgestuurd naar de replica schrijven of naar een alleen-lezen-replica. Met name als de `ApplicationIntent` waarde `ReadWrite` (de standaardwaarde), de verbinding worden omgeleid naar de databasereplica voor lezen / schrijven. Dit is gelijk aan het bestaande gedrag. Als de `ApplicationIntent` waarde `ReadOnly`, de verbinding wordt doorgestuurd naar een alleen-lezen-replica.

De volgende verbindingsreeks maakt de client bijvoorbeeld verbinding met een alleen-lezen replica (de items in de punthaken vervangen door de juiste waarden voor uw omgeving en de punthaken neerzetten):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Een van de volgende tekenreeksen voor databaseverbindingen maakt de client verbinding met een alleen-lezen-replica (de items in de punthaken vervangen door de juiste waarden voor uw omgeving en neer te zetten de punthaken):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

U kunt controleren of u met een alleen-lezen-replica verbonden bent met de volgende query uit te voeren. Alleen-lezen wanneer verbonden met een alleen-lezen replica wordt geretourneerd.


```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```
> [!NOTE]
> Op een bepaald moment is slechts één van de AlwaysON-replica's toegankelijk door de alleen-lezen-sessies.

## <a name="enable-and-disable-read-scale-out"></a>In- en uitschakelen van Read Scale-Out

Read Scale-Out is standaard ingeschakeld in [Managed Instance](sql-database-managed-instance.md) bedrijfskritiek tier(Preview). Het expliciet moet worden ingeschakeld [database op logische server geplaatst](sql-database-logical-servers.md) lagen Premium en bedrijfskritiek. De methoden voor het inschakelen en uitschakelen van Read Scale-Out wordt hier beschreven. 

### <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>In- en uitschakelen van Read Scale-Out met behulp van Azure PowerShell

De December 2016 beheren Read Scale-Out in Azure PowerShell vereist Azure PowerShell versie of hoger. Zie voor de nieuwste versie van PowerShell, [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

In- of uitschakelen van lezen scale-out in Azure PowerShell door het aanroepen van de [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet en aan te in de gewenste waarde – `Enabled` of `Disabled` --voor de `-ReadScale` parameter. U kunt ook de [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet voor het maken van een nieuwe database met lezen scale-out is ingeschakeld.

Bijvoorbeeld, lezen om in te schakelen uitschaling voor een bestaande database (de items in de punthaken vervangen door de juiste waarden voor uw omgeving en de punthaken neerzetten):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Om uit te schakelen lezen scale-out voor een bestaande database (de items in de punthaken vervangen door de juiste waarden voor uw omgeving en de punthaken neerzetten):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Een nieuwe database maken met meer scale-out ingeschakeld (de items in de punthaken vervangen door de juiste waarden voor uw omgeving en de punthaken neerzetten):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Inschakelen en uitschakelen van Read Scale-Out met behulp van de Azure SQL Database REST-API

Voor een database maken met lezen scale-out is ingeschakeld, of voor het in- of uitschakelen van lezen scale-out voor een bestaande database, maken of bijwerken van de bijbehorende database-entiteit met de `readScale` eigenschap ingesteld op `Enabled` of `Disabled` zoals in het onderstaande voorbeeld de aanvraag.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
} 
```

Zie voor meer informatie, [Databases - maken of bijwerken](/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Gebruik Read Scale-Out met geo-replicatie-databases

Als u gebruikmaakt van lezen scale-out laden saldo alleen-lezen-workloads op een database die is geo-replicatie (bijvoorbeeld als een lid van een failovergroep). Zorg ervoor dat meer scale-out is ingeschakeld op zowel de primaire en de geo-replicatie secundaire databases. Hierdoor wordt hetzelfde load balancing effect wanneer uw toepassing verbinding met de nieuwe primaire na een failover maakt. Als u verbinding met de secundaire database met geo-replicatie met leesschaal ingeschakeld maakt, de sessies met `ApplicationIntent=ReadOnly` worden doorgestuurd naar een van de replica's dezelfde manier als we verbindingen routeren op de primaire database.  De sessies zonder `ApplicationIntent=ReadOnly` worden doorgestuurd naar de primaire replica van de secundaire geo-replicatie, die ook is alleen-lezen. Omdat secundaire database via geo-replicatie een ander eindpunt heeft als de primaire database is, in het verleden voor toegang tot de secundaire het is niet vereist om in te stellen `ApplicationIntent=ReadOnly`. Om ervoor te zorgen voor neerwaartse compatibiliteit `sys.geo_replication_links` DMV toont `secondary_allow_connections=2` (alle clientverbinding is toegestaan).

> [!NOTE]
> Tijdens de Preview-versie, round robin of een andere load wordt balanced routering tussen de lokale replica's van de secundaire database niet ondersteund. 


## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het gebruik van PowerShell om in te stellen lezen scale-out, de [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) of de [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlets.
- Zie voor meer informatie over het gebruik van de REST-API om in te stellen lezen scale-out [Databases - maken of bijwerken](/rest/api/sql/databases/createorupdate).
