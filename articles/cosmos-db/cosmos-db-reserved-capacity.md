---
title: Betaal vooruit voor Azure Cosmos DB-resources om geld te besparen | Microsoft Docs
description: Meer informatie over het aanschaffen van Azure Cosmos DB gereserveerde capaciteit om op te slaan op uw rekenkosten.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2291b2429e6c5c25e051c8f3eca30e1cc3f64611
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247326"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Betaal vooruit voor Azure Cosmos DB-resources met gereserveerde capaciteit

Azure Cosmos DB gereserveerde capaciteit kunt die u geld besparen door vooraf betalen voor Azure Cosmos DB-resources voor een periode van één jaar of drie jaar. Azure Cosmos DB gereserveerde capaciteit kunt u krijg een korting op de ingerichte doorvoer voor Cosmos DB-resources, bijvoorbeeld, databases, containers (verzamelingen-tabellen/grafieken). Azure Cosmos DB gereserveerde capaciteit uw Cosmos DB-kosten aanzienlijk kunt verlagen, tot 65 procent op de normale prijzen – met een eenjarige of driejarige toezegging vooraf. Gereserveerde capaciteit biedt een korting van facturering en heeft geen invloed op de runtimestatus van uw Cosmos DB-resources.

Azure Cosmos DB gereserveerde capaciteit geldt voor ingerichte doorvoer voor uw resources, het heeft geen betrekking op de kosten voor opslag en netwerken. Als u een reservering koopt, gaat u de doorvoer kosten in rekening gebracht die overeenkomen met de reservering kenmerken niet meer in tegen de betalen waarbij u gebracht rekening tarieven. Zie voor meer informatie over reserveringen [Azure reserveringen](../billing/billing-save-compute-costs-reservations.md) artikel. 

U kunt Azure Cosmos DB gereserveerde capaciteit uit kopen de [Azure-portal](https://portal.azure.com). Een gereserveerde capaciteit kopen:

* U moet zich in de rol van eigenaar voor ten minste één Enterprise of abonnement op gebruiksbasis.  
* Voor Enterprise-abonnementen, aankopen in de Azure-reservering moeten zijn ingeschakeld in de [EA-portal.](https://ea.azure.com/)  
* Programma Cloud Solution Provider (CSP), kunnen alleen admin-agenten of verkoop agents Azure Cosmos DB gereserveerde capaciteit kopen.

## <a name="determine-the-required-throughput-before-purchase"></a>De vereiste doorvoer voor aankoop bepalen

De grootte van de reservering moet worden gebaseerd op de totale hoeveelheid doorvoer die wordt gebruikt door de bestaande of snel-naar--geïmplementeerd Azure Cosmos DB-resources (bijvoorbeeld, databases of containers - verzamelingen, tabellen, grafieken). U kunt de vereiste doorvoer bepalen in de volgende manieren:

* De historische gegevens ophalen voor de totale ingerichte doorvoer in uw Azure Cosmos DB-accounts, databases en verzamelingen in alle regio's. Bijvoorbeeld, kunt u de dagelijkse gemiddelde ingerichte doorvoer evalueren door uw dagelijkse gebruik overzicht van het downloaden `https://account.azure.com`

* U kunt ook als u een Enterprise Agreement (EA), u kunt uw bestand met gebruiksgegevens downloaden en verwijzen naar **servicetype** waarde in de **aanvullende informatie** gedeelte van het gebruiksbestand om op te halen van de Azure Cosmos DB details van de doorvoer.

* U kunt ook de gemiddelde doorvoer voor alle werkbelastingen optellen van uw Azure Cosmos DB-accounts dat u verwacht wordt uitgevoerd voor de volgende één of drie jaar dat en aantal wordt gebruikt voor de reservering.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB gereserveerde capaciteit kopen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).  

2. Selecteer **alle services** > **reserveringen** > **toevoegen**.  

3. Uit de **Product-Type selecteren** deelvenster kiezen **Azure Cosmos DB**, en vervolgens **Selecteer** naar een nieuwe reservering kopen.  

4. Vul de vereiste velden in zoals beschreven in de onderstaande tabel:

   ![Vul in het formulier gereserveerde capaciteit](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Veld  |Beschrijving  |
   |---------|---------|
   |Naam   |    De naam van de reservering. Dit veld wordt automatisch gevuld met de `CosmosDB_Reservation_<timeStamp>`. U kunt een andere naam geven tijdens het maken van de reservering of wijzig de naam nadat de reservering is gemaakt.      |
   |Abonnement  |   Het abonnement dat wordt gebruikt om te betalen voor de Azure Cosmos DB gereserveerde capaciteit. De betalingswijze voor het geselecteerde abonnement wordt gebruikt in de kosten vooraf in rekening gebracht. Het abonnementstype moet een van de volgende: <br/><br/>  [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (nummer van aanbieding: MS-AZR - 0017 P)-voor een enterprise-abonnement, de kosten zijn in mindering gebracht op de monetaire toezeggingsbedrag van de inschrijving of kosten in rekening gebracht als overschrijding. <br/><br/> [Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) (nummer van aanbieding: MS-AZR - 0003 P)-voor betalen per gebruik-abonnement, de kosten worden in rekening gebracht op de betalingsmethode creditcard of per factuur voor het abonnement.    |
   |Bereik   |   Bereik van de reservering bepaalt het aantal abonnementen kunnen gebruikmaken van de facturering voordeel dat is gekoppeld aan de reservering, en het bepaalt hoe de reservering wordt toegepast op bepaalde abonnementen. Een reservering heeft een bereik van één of gedeeld abonnement. Als u selecteert:   <br/><br/>  **Enkel abonnement** -de reserveringskorting wordt toegepast op Azure Cosmos DB-exemplaren in het geselecteerde abonnement. <br/><br/>  **Gedeelde** – de reserveringskorting wordt toegepast op Azure Cosmos DB-instanties die actief zijn in alle abonnementen in de context van de facturering. De context van de facturering wordt bepaald op basis van hoe u zich hebt geregistreerd voor Azure. Voor zakelijke klanten, gedeeld bereik is van de inschrijving en bevat alle abonnementen (met uitzondering van dev/test-abonnementen) binnen de inschrijving. Voor klanten van betalen per gebruik is het gedeelde bereik alle betalen per gebruik-abonnementen die zijn gemaakt door de accountbeheerder.  <br/><br/> U kunt het reserveringsbereik wijzigen na de aankoop van de gereserveerde capaciteit.  |
   |Type gereserveerde capaciteit   |  Type gereserveerde capaciteit is de doorvoer die is ingericht in termen van aanvraageenheden.|
   |Eenheden gereserveerde capaciteit  |      De hoeveelheid doorvoer die u wilt reserveren. U kunt deze waarde berekenen door te bepalen van de doorvoer die nodig zijn voor al uw Cosmos DB-resources (bijvoorbeeld, databases of containers) per regio en vervolgens vermenigvuldigd met het aantal regio's die u aan uw Cosmos DB-database koppelen wilt.  <br/> Bijvoorbeeld: als u vijf regio's met 1 miljoen RU/sec. in elke regio hebt, selecteert u 5 miljoen RU/sec. voor capaciteit kopen van reserveringen.    |
   |Termijn  |   Één of drie jaar.   |

5. Bekijk de korting en de prijs van de reservering in de **kosten** sectie. Deze reservering prijs is van toepassing op Azure Cosmos DB-resources met doorvoer die is ingericht in alle regio's.  

6. Selecteer **Aankoop**. U kunt de volgende schermafbeelding zien wanneer de aankoop voltooid is. 

   ![Vul in het formulier gereserveerde capaciteit](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Nadat u een reservering koopt, wordt deze onmiddellijk toegepast op bestaande Azure Cosmos DB-resources die overeenkomen met de voorwaarden van de reservering. Als u geen bestaande Azure Cosmos DB-resources, wordt de reservering wordt toegepast wanneer u een nieuwe Cosmos DB-exemplaar dat overeenkomt met de voorwaarden van de reservering implementeert. In beide gevallen wordt de periode van de reservering direct na een succesvolle aankoop gestart. 

Wanneer uw reservering verloopt, wordt uw Azure Cosmos DB-instanties wordt nog uitgevoerd en worden gefactureerd tegen de normale betalen per gebruik-tarieven betalen.

## <a name="next-steps"></a>Volgende stappen

De reserveringskorting wordt automatisch toegepast op de Azure Cosmos DB-resources die overeenkomen met het reserveringsbereik en kenmerken. U kunt het bereik van de reservering via Azure portal, PowerShell, CLI, of via de API bijwerken.

*  Zie voor meer informatie hoe gereserveerde capaciteit kortingen zijn toegepast op Azure Cosmos DB, [korting op Azure-reserveringen te begrijpen](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Zie voor meer informatie over Azure reserveringen, de volgende artikelen:

   * [Wat zijn Azure reserveringen?](../billing/billing-save-compute-costs-reservations.md)  
   * [Azure-reserveringen beheren](../billing/billing-manage-reserved-vm-instance.md).  
   * [Inzicht in gebruik van de reservering voor uw Enterprise-inschrijving](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Gebruik van de reservering voor uw abonnement op gebruiksbasis begrijpen](../billing/billing-understand-reserved-instance-usage.md)
   * [Azure-reserveringen in programma Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u nog meer vragen hebt, [contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel worden opgelost.

