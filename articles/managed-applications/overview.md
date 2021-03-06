---
title: Overzicht van Azure Managed Applications | Microsoft Docs
description: Beschrijft de concepten van Azure Managed Applications
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: 628a936d85eb94a1ee332205047527b0f9795d50
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990511"
---
# <a name="azure-managed-applications-overview"></a>Overzicht van Azure Managed Applications

Met Azure Managed Applications kunt cloudoplossingen bieden die consumenten eenvoudig kunnen implementeren en gebruiken. U implementeert de infrastructuur en biedt blijvende ondersteuning. Als u een beheerde toepassing beschikbaar wilt stellen voor alle klanten, publiceert u de toepassing in de Azure Marketplace. Als u de toepassing alleen beschikbaar wilt stellen voor gebruikers in uw organisatie, publiceert u de toepassing in een interne catalogus. 

Een beheerde toepassing is vergelijkbaar met een oplossingssjabloon in de Microsoft Azure Marketplace, met één belangrijk verschil. In een beheerde toepassing worden de resources ingericht vanuit een resourcegroep die wordt beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. De uitgever bepaalt de kosten voor de voortdurende ondersteuning van de oplossing.

## <a name="advantages-of-managed-applications"></a>Voordelen van beheerde toepassingen

Beheerde toepassingen verminderen de barrières voor consumenten om uw oplossingen te gebruiken. Ze hebben geen kennis van de cloudinfrastructuur nodig om uw oplossing te kunnen gebruiken. Gebruikers hebben beperkte toegang tot kritieke bronnen. Het is niet erg als er een fout wordt gemaakt in het beheer ervan. 

Met beheerde toepassingen kunt u een langdurige relatie tot stand brengen met uw consumenten. U bepaalt de voorwaarden voor het beheer van de toepassing en alle kosten worden afgehandeld via Azure Billing.

Hoewel klanten deze beheerde toepassingen implementeren in hun abonnementen, hoeven ze deze niet te onderhouden of bij te werken. U kunt ervoor zorgen dat alle klanten goedgekeurde versies gebruiken. Klanten hoeven geen toepassingsspecifieke domeinkennis te ontwikkelen om deze toepassingen te beheren. Klanten verkrijgen automatisch toepassingsupdates en hoeven zich geen zorgen te maken over probleemoplossing en diagnoseproblemen met de toepassingen. 

Met beheerde toepassingen kunnen IT-teams de gebruikers in hun organisatie vooraf goedgekeurde oplossingen aanbieden. U zorgt ervoor dat deze oplossingen voldoen aan de organisatiestandaarden.

## <a name="types-of-managed-applications"></a>Soorten beheerde toepassingen

U kunt uw beheerde toepassing zowel extern als intern publiceren.

![Intern of extern publiceren](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Servicecatalogus

De servicecatalogus is een interne catalogus van goedgekeurde oplossingen voor gebruikers in een organisatie. U gebruikt de catalogus om naleving van bepaalde organisatiestandaarden te garanderen en tegelijkertijd oplossingen voor de organisaties te bieden. Werknemers gebruiken de catalogus om eenvoudig toepassingen te bekijken die worden aanbevolen en zijn goedgekeurd door hun IT-afdelingen. Ze zien welke beheerde toepassingen andere personen in hun organisatie met hen delen.

Zie [Een beheerde toepassing voor intern verbruik publiceren](publish-service-catalog-app.md) voor meer informatie over het publiceren van beheerde toepassing in een servicecatalogus.

### <a name="marketplace"></a>Marketplace

Leveranciers die voor hun services betaald willen krijgen, kunnen een beheerde toepassing beschikbaar maken via de Azure Marketplace. Nadat de leverancier een toepassing heeft gepubliceerd, is deze beschikbaar voor gebruikers buiten de organisatie. Op deze manier kunnen MSP's (providers van beheerde services), ISV's (onafhankelijke softwareleveranciers) en SI's (systeemintegrators) hun oplossingen aanbieden aan alle Azure-klanten.

Zie [Marketplace-toepassing maken](publish-marketplace-app.md) voor meer informatie over het publiceren van beheerde toepassingen in de Microsoft Azure Marketplace.

## <a name="resource-groups-for-managed-applications"></a>Resourcegroepen voor beheerde toepassingen

De resources voor een beheerde toepassing bevinden zich doorgaans in twee resourcegroepen. De consument beheert één resourcegroep en de uitgever beheert de andere. Bij het definiëren van de beheerde toepassing bepaalt de uitgever de toegangsniveaus. Het beperken van de toegang voor [gegevensbewerkingen](../role-based-access-control/role-definitions.md) wordt momenteel niet voor alle gegevensproviders in Azure ondersteund.

In de volgende afbeelding ziet u een scenario waarbij de uitgever de rol van eigenaar voor de beheerde resourcegroep aanvraagt. De uitgever plaatst een alleen-lezen-vergrendeling op deze resourcegroep voor de consument. De uitgeversidentiteiten die toegang tot de beheerde resourcegroep krijgen, zijn uitgesloten van de vergrendeling.

![Toegang tot resourcegroep](./media/overview/access.png)

### <a name="application-resource-group"></a>Resourcegroep van toepassing

Deze resourcegroep bevat het exemplaar van de beheerde toepassing. Deze resourcegroep mag maar één resource bevatten. Het resourcetype van de beheerde toepassing is **Microsoft.Solutions/applications**.

De consument heeft volledige toegang tot de resourcegroep en gebruikt deze om de levenscyclus van de beheerde toepassing te beheren.

### <a name="managed-resource-group"></a>Beheerde resourcegroep

Deze resourcegroep bevat alle resources die de beheerde toepassing nodig heeft. Deze resourcegroep bevat bijvoorbeeld de virtuele machines, opslagaccounts en virtuele netwerken voor de oplossing. De consument heeft beperkte toegang tot deze resourcegroep omdat de consument de afzonderlijke resources voor de beheerde toepassing niet beheert. De toegang van de uitgever tot deze resourcegroep komt overeen met de rol die is opgegeven in de definitie van de beheerde toepassing. De uitgever kan bijvoorbeeld de rol van eigenaar of bijdrager voor deze resourcegroep aanvragen.

Wanneer de gebruiker de beheerde toepassing verwijdert, wordt ook de beheerde resourcegroep verwijderd.

## <a name="azure-policy"></a>Azure Policy

U kunt een [Azure Policy](../azure-policy/azure-policy-introduction.md) toepassen op uw beheerde toepassing. U past beleidsregels toe om te verzekeren dat geïmplementeerde exemplaren van uw beheerde toepassing aan gegevens- en beveiligingsvereisten voldoen. Als uw toepassing met gevoelige gegevens werkt, zorg er dan voor dat u hebt geëvalueerd hoe die moeten worden beschermd. Als uw toepassing bijvoorbeeld met gegevens uit Office 365 werkt, past u een beleid toe om te verzekeren dat gegevensversleuteling is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

* Voor een inleiding tot het definiëren en implementeren van beheerde toepassingen raadpleegt u [Een beheerde Azure-toepassing maken en implementeren met Azure CLI](managed-apps-quickstart-cli.md)
* Zie [Een beheerde toepassing voor intern verbruik publiceren](publish-service-catalog-app.md) voor meer informatie over het publiceren van een interne toepassing.
* Zie [Marketplace-toepassing maken](publish-marketplace-app.md) voor meer informatie over het uitgeven van beheerde toepassingen in de marketplace.
