---
title: Een toegangscontrole voor Azure resource-rollen in PIM starten | Microsoft Docs
description: Leer hoe u een toegangscontrole voor Azure-resourcerollen starten in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9a35d32d89931a03b33f232ba4f79226fc3f57e5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189171"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>Een toegangscontrole voor Azure resource-rollen in PIM starten
Roltoewijzingen worden 'verouderde' wanneer gebruikers uitgebreide toegang die ze niet meer nodig hebt. Beheerders met bevoegdheid om het risico dat is gekoppeld aan deze verouderde roltoewijzingen, moeten regelmatig rollen controleren. In dit document bevat informatie over de stappen voor het starten van een toegangscontrole in Privileged Identity Management (PIM) voor Azure-resources.

Op de toepassing hoofdpagina van PIM, gaat u naar:

* **Toegangsbeoordelingen** > **toevoegen**

![Toegangsbeoordelingen toevoegen](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Wanneer u selecteert de **toevoegen** knop, de **een toegangsbeoordeling maken** blade wordt weergegeven. De controle configureren met een naam en een tijdslimiet, kiest u een rol om te controleren en vervolgens besluit die de beoordeling is op deze blade.

![Een toegangsbeoordeling maken](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configureren van de beoordeling
Voor het maken van een toegangscontrole, geef het eerst de naam en stel een begin- en datum.

![Configureren van controle - schermafbeelding](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Controleer de lengte van de beoordeling lang genoeg is voor gebruikers om deze te voltooien. Als ze klaar zijn voor de einddatum vallen, kunnen ze de beoordeling vroeg altijd stoppen.

### <a name="choose-a-role-to-review"></a>Kies een rol om te controleren
Elke beoordeling die is gericht op slechts één rol. Tenzij u de toegangsbeoordeling vanuit de blade voor een specifieke rol gestart, moet u nu een rol kiezen.

1. Ga naar **rollidmaatschap beoordelen**
   
    ![Rollidmaatschap controle - schermafbeelding](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Kies één rol in de lijst.

### <a name="decide-who-will-perform-the-review"></a>Bepalen wie verantwoordelijk is voor de beoordeling
Er zijn drie opties voor het uitvoeren van een beoordeling. U kunt de beoordeling toewijzen aan iemand anders om te voltooien, zelf doen of elke gebruiker een eigen toegang kunt bekijken.

1. Kies een van de opties:
   
   * **Geselecteerde gebruikers**: Gebruik deze optie als u niet weet die toegang nodig heeft. Met deze optie kunt u de controle toewijzen aan een resource-eigenaar of groepmanager om te voltooien.
   * **Toegewezen (zelf)**: Gebruik deze optie om de gebruikers hun eigen roltoewijzingen bekijken.
   
2. Ga naar **beoordelaars selecteren**.
   
    ![Selecteer revisoren - schermafbeelding](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>De revisie starten
Ten slotte kunt u vereisen dat gebruikers een reden voor het goedkeuren van toegang bieden. Een beschrijving van de beoordeling als u dat wilt toevoegen. Selecteer vervolgens **Start**.

Zorg ervoor dat u uw gebruikers weten dat er een toegangsbeoordeling wachten tot ze en ze weergeven brengt [hoe u een toegangscontrole uitvoeren](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Beheren van de toegangsbeoordeling
In het dashboard PIM-Azure-resources kunt u de voortgang volgen de revisoren hun beoordelingen zijn voltooid. Er is geen toegangsrechten worden gewijzigd in de map tot [de beoordeling is voltooid](pim-resource-roles-complete-access-review.md).

Totdat de periode van de toegangsbeoordeling voltooid is, kunt u Herinner de gebruikers om uit te voeren van de beoordeling of stoppen van de beoordeling vroeg in het gedeelte van de beoordelingen toegang.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangscontrole voor Azure-resource-rollen in PIM voltooien](pim-resource-roles-complete-access-review.md)
- [Een toegangscontrole van mijn Azure-resource-rollen in PIM uitvoeren](pim-resource-roles-perform-access-review.md)
- [Een toegangscontrole voor Azure AD directory-rollen in PIM starten](pim-how-to-start-security-review.md)
