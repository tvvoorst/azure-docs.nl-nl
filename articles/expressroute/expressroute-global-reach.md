---
title: Over ExpressRoute wereldwijd bereik | Microsoft Docs
description: In dit artikel wordt uitgelegd wat ExpressRoute globaal bereik is.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: c96841274f73be766b0157f036746599cc836dd3
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068040"
---
# <a name="expressroute-global-reach-preview"></a>ExpressRoute wereldwijd bereik (Preview)
ExpressRoute is een privé- en flexibele manier om uw on-premises netwerken verbinden met Microsoft Cloud. U kunt toegang tot veel Microsoft-cloudservices zoals Azure, Office 365 en Dynamics 365 vanuit uw persoonlijke Datacenter of uw bedrijfsnetwerk. Bijvoorbeeld, wellicht u een filiaal in San Francisco met een ExpressRoute-circuit in Silicon Valley en een ander filiaal in Londen met een ExpressRoute-circuit in dezelfde stad. Beide filialen kunnen zeer snelle verbindingen met Azure-resources in VS West en UK-Zuid hebben. Echter kunnen geen de filialen uitwisselen van gegevens met elkaar. Met andere woorden, 10.0.1.0/24 die gegevens kan verzenden, 10.0.3.0/24 en 10.0.4.0/24, maar niet aan 10.0.2.0/24.

![zonder][1]

Met **ExpressRoute globaal bereik**, kunt u ExpressRoute-circuits samen om te maken van een particulier netwerk tussen uw on-premises netwerken koppelen. In het bovenstaande voorbeeld wordt met de toevoeging van ExpressRoute globaal bereik, kan uw kantoor in San Francisco (10.0.1.0/24) rechtstreeks uitwisselen van gegevens met uw Londen office (10.0.2.0/24) via de bestaande ExpressRoute-circuits en via het wereldwijde netwerk van Microsoft. 

![met][2]

## <a name="use-case"></a>Toepassing
ExpressRoute globaal bereik is ontworpen om een aanvulling vormen op de WAN-implementatie van de serviceprovider en verbinding maken met uw filialen over de hele wereld. Bijvoorbeeld, als uw serviceprovider voornamelijk werkt in de Verenigde Staten en al uw vertakkingen in de VS is gekoppeld, maar de service-provider niet in Japan en Hong Kong werkt, met globaal bereik van ExpressRoute kunt u werken met een lokale service-provider en Microsoft maakt uw vertakkingen er verbinding met de namen in de Verenigde Staten met behulp van ExpressRoute en ons wereldwijde netwerk.

![Use-case][3]

## <a name="availability"></a>Beschikbaarheid 
ExpressRoute globaal bereik wordt op dat moment ondersteund in de volgende locaties.

* Verenigde Staten
* Verenigd Koninkrijk 
* Ierland
* Nederland
* Australië
* Hongkong 
* Japan

Uw ExpressRoute-circuits moeten worden gemaakt op de [ExpressRoute-peeringlocaties](expressroute-locations.md) in de bovenstaande landen of regio. Om in te schakelen globaal bereik van ExpressRoute tussen [verschillende geopolitieke regio's](expressroute-locations.md), uw circuits moet Premium-SKU.

## <a name="next-steps"></a>Volgende stappen
1. [Meer informatie over ExpressRoute globaal bereik](expressroute-faqs.md)
2. [Het inschakelen van ExpressRoute globaal bereik](expressroute-howto-set-global-reach.md)
3. [ExpressRoute-circuit koppelen met Azure-netwerk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram zonder wereldwijd bereik"
[2]: ./media/expressroute-global-reach/2.png "diagram met globaal bereik"
[3]: ./media/expressroute-global-reach/3.png "use-case van wereldwijd bereik"
