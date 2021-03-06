---
title: Gegevens importeren voor gebruik met de Azure Cosmos DB Table-API | Microsoft Docs
description: Meer informatie over het importeren van gegevens voor gebruik met de Azure Cosmos DB Table-API.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2017
ms.author: sngun
ms.openlocfilehash: 905815259707116759e0b980690fac108ab81c7b
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186827"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Gegevens migreren naar een Azure Cosmos DB Table-API-account

In deze zelfstudie vindt u instructies voor het importeren van gegevens voor gebruik met de Azure Cosmos DB [Table-API](table-introduction.md). Als u gegevens hebt opgeslagen in Azure Table Storage, kunt u het gegevensmigratieprogramma of AzCopy gebruiken om de gegevens te importeren in Azure Cosmos DB Table-API. Als u gegevens hebt opgeslagen in een Azure Cosmos DB Table-API-account (preview), moet u het gegevensmigratieprogramma gebruiken om uw gegevens te migreren. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API 

## <a name="prerequisites"></a>Vereisten

* Verhoog de doorvoer: de duur van de gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u voor een afzonderlijke container of een reeks containers instelt. Verhoog de doorvoer voor grotere gegevensmigraties. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen. Zie Prestatieniveaus en prijscategorieën in Azure Cosmos DB voor meer informatie over het verhogen van de doorvoer in Azure Portal.

## <a name="data-migration-tool"></a>Gegevensmigratieprogramma

Het opdrachtregelprogramma voor Azure Cosmos DB-gegevensmigratie (dt.exe) kan worden gebruikt om uw bestaande Azure Table Storage-gegevens te importeren in een account voor een algemeen beschikbare Table-API of om gegevens te migreren van een Table-API-account (preview) naar een account voor een algemeen beschikbare Table-API. Andere bronnen worden momenteel niet ondersteund. Het gegevensmigratieprogramma dat is gebaseerd op een gebruikersinterface (dtui.exe) wordt momenteel niet ondersteund voor Table-API-accounts. 

Als u tabelgegevens wilt migreren, moet u de volgende taken uitvoeren:

1. Download het migratieprogramma op [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Voer `dt.exe` uit met de opdrachtregelargumenten voor uw scenario. `dt.exe` wordt uitgevoerd met een opdracht in de volgende indeling:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
```

Opties voor de opdracht zijn:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Broninstellingen voor de opdrachtregel

Gebruik de volgende bronopties bij het definiëren van Azure Table Storage of Table-API preview als de bron van de migratie.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Als u de verbindingsreeks voor de bron wilt ophalen bij het importeren uit Azure Table Storage, opent u Azure Portal, klikt u op **Storage-accounts** > **Account** > **Toegangssleutels** en kopieert u vervolgens de **verbindingsreeks** met de knop Kopiëren.

![Schermopname van HBase-bronopties](./media/table-import/storage-table-access-key.png)

Als u de verbindingsreeks voor de bron wilt ophalen bij het importeren uit een Azure Cosmos DB Table-API-account (preview), opent u Azure Portal, klikt u op **Azure Cosmos DB** > **Account** > **Verbindingsreeks** en kopieert u vervolgens de **verbindingsreeks** met de knop Kopiëren.

![Schermopname van HBase-bronopties](./media/table-import/cosmos-connection-string.png)

[Voorbeeld van Azure Table Storage-opdracht](#azure-table-storage)

[Voorbeeld van opdracht voor Azure Cosmos DB Table-API (preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Doelinstellingen voor opdrachtregel

Gebruik de volgende doelopties bij het definiëren van de Azure Cosmos DB Table-API als het doel van de migratie.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Voorbeeldopdracht: bron is Azure Table Storage

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Azure Table Storage in Table-API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Voorbeeldopdracht: bron is Azure Cosmos DB Table-API (preview)

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Table-API preview in een algemeen beschikbare Table-API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Gegevens migreren met AzCopy

U kunt ook met het AzCopy-opdrachtregelprogramma gegevens vanuit Azure Table Storage migreren naar de Azure Cosmos DB Table-API. Als u AzCopy wilt gebruiken, exporteert u eerst de gegevens zoals wordt beschreven in [Gegevens exporteren vanuit Azure Table Storage](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) en importeert u de gegevens vervolgens in Azure Cosmos DB zoals wordt beschreven in [Azure Cosmos DB Table-API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Bij het importeren in Azure Cosmos DB kunt u de volgende opdracht als voorbeeld nemen. Houd er rekening mee dat de waarde voor /Dest cosmosdb is en niet core.

Voorbeeld van opdracht voor importeren:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migreren vanuit Table-API (preview) naar Table-API

> [!WARNING]
> Als u direct voordeel wilt hebben van de algemeen beschikbare tabellen, migreert u uw bestaande previewtabellen zoals in deze sectie is beschreven. Anders worden de tabellen de komende weken automatisch gemigreerd voor bestaande previewklanten. Houd er echter rekening mee dat er voor automatisch gemigreerde previewtabellen bepaalde beperkingen gelden die niet van toepassing zijn op onlangs gemaakte tabellen.
> 

De Table-API is nu algemeen beschikbaar. Er zijn verschillen tussen de previewversie en de algemeen beschikbare versie voor tabellen in de code die wordt uitgevoerd in de cloud en in de code die wordt uitgevoerd op de client. Daarom wordt het niet aangeraden om een preview SDK-client te combineren met een account voor een algemeen beschikbare Table-API. Als klanten van de Table-API preview de bestaande tabellen willen blijven gebruiken in een productieomgeving, moeten ze de tabellen vanuit de previewomgeving migreren naar de omgeving met een algemeen beschikbare Table-API of wachten totdat de tabellen automatisch worden gemigreerd. Als u wacht op de automatische migratie, krijgt u een melding over de beperkingen die voor de gemigreerde tabellen gelden. Na de migratie kunt u voor uw bestaande account nieuwe tabellen zonder beperkingen maken (alleen gemigreerde tabellen hebben beperkingen).

U migreert als volgt van Table-API (preview) naar de algemeen beschikbare Table-API:

1. Maak een nieuw Azure Cosmos DB-account en stel hiervoor het API-type in op Azure Table, zoals wordt beschreven in [Een databaseaccount maken](create-table-dotnet.md#create-a-database-account).

2. Zorg ervoor dat de clients gebruikmaken van een algemeen beschikbare release van de [Table-API-SDK's](table-sdk-dotnet.md).

3. Migreer de clientgegevens van de previewtabellen naar de algemeen beschikbare tabellen met behulp van het gegevensmigratieprogramma. Instructies voor het gebruik van het gegevensmigratieprogramma voor dit doeleinde worden beschreven in [Gegevensmigratieprogramma](#data-migration-tool). 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API

U kunt nu verdergaan met de volgende zelfstudie om te leren hoe u query's uitvoert op gegevens met de Azure Cosmos DB Table-API. 

> [!div class="nextstepaction"]
>[Hoe kan ik query’s uitvoeren op gegevens?](../cosmos-db/tutorial-query-table.md)
