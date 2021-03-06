---
title: Serverlogboeken voor Azure Database for MySQL
description: Beschrijving van de logboeken die beschikbaar zijn in Azure Database voor MySQL en de beschikbare parameters voor het inschakelen van van verschillende logboekregistratieniveaus.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: ac5be20815b552c08e5cd1054bf24d7a10b56498
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124266"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Serverlogboeken in Azure Database for MySQL
In Azure Database voor MySQL is het logboek voor langzame query's beschikbaar voor gebruikers. Toegang tot het transactielogboek wordt niet ondersteund. Het logboek voor langzame query's kan worden gebruikt om knelpunten in de prestaties voor het oplossen van problemen. 

Zie voor meer informatie over het MySQL-logboek voor langzame query's, de MySQL-handleiding [uplogboekgedeelte van de query vertragen](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Serverlogboeken openen
U kunt weergeven en downloaden van Azure Database voor MySQL-server-logboeken met behulp van de Azure-portal en de Azure CLI.

Selecteer uw Azure Database voor MySQL-server in de Azure-portal. Onder de **bewaking** kop, selecteer de **serverlogboeken** pagina.

Zie voor meer informatie over Azure-CLI [configureren en -server-logboeken met behulp van Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Logboekbehoud
Logboeken zijn beschikbaar voor maximaal zeven dagen van hun maken. Als de totale grootte van de beschikbare logboeken 7 GB overschrijdt, worden de oudste bestanden verwijderd totdat ruimte beschikbaar is. 

Logboeken zijn geroteerd elke 24 uur of 7 GB, afhankelijk van wat eerst komt.


## <a name="configure-logging"></a>Logboekregistratie configureren 
Het logboek voor langzame query's is standaard uitgeschakeld. Als u wilt inschakelen, stelt u slow_query_log op ON.

Er zijn andere parameters die u kunt aanpassen:

- **long_query_time**: als een query langer duurt dan long_query_time (in seconden) deze query wordt geregistreerd. De standaardwaarde is 10 seconden.
- **log_slow_admin_statements**: als u op administratieve instructies zoals ALTER_TABLE en ANALYZE_TABLE in de instructies naar de slow_query_log geschreven bevat.
- **log_queries_not_using_indexes**: Hiermee bepaalt u of query's die geen gebruik maken van indexen worden geregistreerd in de slow_query_log
- **log_throttle_queries_not_using_indexes**: deze parameter beperkt het aantal niet-index-query's die kunnen worden geschreven naar het logboek voor langzame query's. Deze parameter wordt van kracht wanneer log_queries_not_using_indexes is ingesteld op ON.

Zie de MySQL [trage query-documentatie voor log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) voor een volledige beschrijving van de parameters voor het vastleggen van langzame query's.

## <a name="next-steps"></a>Volgende stappen
- [Over het configureren en serverlogboeken openen vanuit de Azure CLI](howto-configure-server-logs-in-cli.md).
