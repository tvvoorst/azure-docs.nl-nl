---
title: Web application firewall aanvraaglimieten grootte en uitsluitingslijsten in Azure Application Gateway - Azure portal | Microsoft Docs
description: In dit artikel bevat informatie over web application firewall-aanvraaglimieten grootte en uitsluiting geeft een lijst van de configuratie in Application Gateway met Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965490"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Web application firewall-aanvraaglimieten grootte- en uitsluitingsbeleid geeft een lijst van (openbare Preview)

De Azure Application Gateway web application firewall (WAF) biedt beveiliging voor webtoepassingen. Dit artikel wordt beschreven WAF grootte aanvraaglimieten en uitsluiting geeft een lijst van de configuratie.

> [!IMPORTANT]
> Configuratie van de grootte van WAF aanvraaglimieten en uitsluitingslijsten is momenteel in openbare preview. Deze preview wordt aangeboden zonder een service level agreement, en wordt niet aanbevolen voor productieworkloads. Bepaalde functies mogelijk niet ondersteund of mogelijk beperkt. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Maximale grootte van WAF-aanvraag
Web Application Firewall kunnen gebruikers grootte aanvraaglimieten in kleine en bovengrens configureren. De volgende configuraties van de grootte van de twee limieten zijn beschikbaar:
- Het veld voor maximale aanvraag hoofdtekst van de grootte is opgegeven in kB's en besturingselementen algemene aanvraag grootte, met uitzondering van elk bestand wordt geüpload. Dit veld kan variëren van 1 KB minimale aan de maximumwaarde 128 KB. De standaardwaarde voor de aanvraag hoofdtekst van de grootte is 128 KB.
- Het veld met bestand uploaden limiet is opgegeven in MB en deze verklaring regelt de maximum toegestane bestandsgrootte voor uploaden. Dit veld kan hebben een minimumwaarde van 1 MB en maximaal 500 MB. De standaardwaarde voor bestand uploadlimiet is 100 MB.

WAF biedt ook een knop kunnen worden geconfigureerd om in te schakelen van de controle van de hoofdtekst van de aanvraag voor in- of uitschakelen. De aanvraag hoofdtekst van de controle is standaard ingeschakeld. Als de aanvraag hoofdtekst van de controle is uitgeschakeld, WAF niet de inhoud van de hoofdtekst van de HTTP-bericht worden geëvalueerd. In dergelijke gevallen blijft WAF WAF-regels op headers, cookies en URI afgedwongen. Als de aanvraag hoofdtekst van de controle is uitgeschakeld, veld voor maximale aanvraag hoofdtekst van de grootte is niet van toepassing en kan niet worden ingesteld. Het uitschakelen van de aanvraag hoofdtekst controle mogelijk voor berichten die groter zijn dan 128 KB worden verzonden naar WAF. De berichttekst is echter niet gecontroleerd op beveiligingsproblemen.

## <a name="waf-exclusion-lists"></a>WAF-uitsluitingslijsten

WAF-uitsluitingslijsten toestaan dat gebruikers bepaalde kenmerken van een evaluatie WAF weglaten. Een veelvoorkomend voorbeeld is de dat Active Directory-tokens die worden gebruikt voor verificatie of wachtwoordvelden ingevoegd. Deze kenmerken zijn gevoelig zijn voor de speciale tekens bevatten die een fout-positief vanaf de WAF-regels kunnen activeren. Wanneer een kenmerk is toegevoegd aan de uitsluitingslijst WAF, is deze niet in aanmerking genomen door alle geconfigureerde en actieve WAF-regel. Uitsluitingslijsten zijn globaal binnen het bereik.
U kunt aanvraagheaders, cookies van de aanvraag en aanvraag query tekenreeksargumenten toevoegen aan uitsluitingslijsten WAF. U kunt opgeven als een exacte header, cookie of query kenmerk tekenreeksovereenkomst aan te vragen of eventueel gedeeltelijke overeenkomsten kunt opgeven. Hier volgen de ondersteunde overeenkomst criteria operators: 
- **Is gelijk aan**: deze operator wordt gebruikt voor een exacte overeenkomst. Een voorbeeld: voor het selecteren van de koptekst met de naam **bearerToken**"** gebruiken met selector instellen als operator is gelijk aan **bearerToken**. 
- **Begint met**: deze operator komt overeen met alle velden die met de selectie van de opgegeven waarde beginnen. 
- **Eindigt met**: deze operator komt overeen met alle velden op aanvraag die met de selectie van de opgegeven waarde eindigen. 
- **Bevat**: deze operator komt overeen met alle velden voor aanvraag die de selectie van de opgegeven waarde bevatten.

In alle gevallen wordt onderscheid en reguliere expressies zijn niet toegestaan als het selectoren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen configureert, leert u hoe u om uw WAF-logboeken weer te geven. Zie voor meer informatie, [Application Gateway diagnostics](application-gateway-diagnostics.md#diagnostic-logging).