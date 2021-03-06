---
title: Azure Application Insights telemetrie Data Model - gebeurtenis telemetrie | Microsoft Docs
description: Application Insights-gegevensmodel voor telemetrie van gebeurtenissen
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 062478783465edc2d3afa4b80a22f119e68da049
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091703"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie van gebeurtenissen: Application Insights-gegevensmodel

U kunt gebeurtenis maken telemetrie-items (in [Application Insights](app-insights-overview.md)) om weer te geven van een gebeurtenis die heeft plaatsgevonden in uw toepassing. Het is doorgaans een gebruikersinteractie zoals knop klikt u op of rangschikken kassa. Een gebeurtenis van de levenscyclus van de toepassing, zoals het bijwerken van de initialisatie of configuratie kan ook zijn. 

Semantisch, gebeurtenissen kunnen of kunnen niet worden gecorreleerd met aanvragen. Als echter correct hebt gebruikt, telemetrie van gebeurtenissen is belangrijker dan aanvragen of traceringen. Gebeurtenissen vertegenwoordigen de zakelijke Telemetrie en moet een onderwerp te scheiden, minder agressief [steekproeven](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Naam

De naam van de gebeurtenis. Als u wilt toestaan dat de juiste groepering en nuttige metrische gegevens, uw toepassing zodanig beperken dat er een klein aantal gebeurtenisnamen van afzonderlijke wordt gegenereerd. Bijvoorbeeld, gebruik niet de naam van een afzonderlijk voor elk gegenereerde exemplaar van een gebeurtenis.

Maximale lengte: 512 tekens

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevensmodel](application-insights-data-model.md) voor Application Insights-typen en -gegevensmodel.
- [Telemetrie van aangepaste gebeurtenissen schrijven](app-insights-api-custom-events-metrics.md#trackevent)
- Bekijk [platforms](app-insights-platforms.md) ondersteund door Application Insights.
