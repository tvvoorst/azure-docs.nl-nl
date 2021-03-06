---
title: Selfservice of proefversie registreren in Azure Active Directory | Microsoft Docs
description: Aanmelding via selfservice gebruiken in een tenant Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 99c5e99fa3bd33ef42e8df6ceba5be4be2cd1249
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871759"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Wat is selfserviceregistratie voor Azure Active Directory?
In dit artikel wordt uitgelegd Self-serviceregistratie en hoe u voor de ondersteuning in Azure Active Directory (Azure AD). Als u wilt een domeinnaam overnemen van een niet-beheerde Azure AD-tenant, Zie [overnemen van een niet-beheerde adreslijst als administrator](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Waarom Self-serviceregistratie gebruiken?
* Breng uw klanten naar services die ze sneller willen
* Aanbiedingen op basis van een e-mailadres voor een service maken
* Aanmelding op basis van een e-stromen die snel gebruikers toestaan te maken met behulp van hun e-mailadres werk gemakkelijk te onthouden aliassen identiteiten maken
* Een zelf-Self-service gemaakte Azure AD-directory kan worden omgezet in een beheerde adreslijst die kan worden gebruikt voor andere services

## <a name="terms-and-definitions"></a>Termen en definities
* **Self-serviceregistratie**: dit is de methode waarmee een gebruiker zich aanmeldt voor een service in de cloud en heeft een identiteit die automatisch voor ze zijn gemaakt in Azure AD op basis van hun e-maildomein.
* **Niet-beheerde Azure AD-directory**: dit is de map waar deze identiteit is gemaakt. Een niet-beheerde adreslijst is een map die geen globale beheerder heeft.
* **Via e-mail geverifieerde gebruiker**: dit is een type gebruikersaccount in Azure AD. Een gebruiker met een automatisch gemaakt nadat het aanmelden voor een aanbieding selfservice staat bekend als een gebruiker via e-mail geverifieerde identiteit. Een gebruiker via e-mail geverifieerde reguliere lid is van een map die is gemarkeerd met creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hoe beheers ik selfservice-instellingen
Beheerders hebben nu twee besturingselementen voor selfservice. Ze kunnen bepalen of:

* Gebruikers kunnen deelnemen aan de directory via e-mail.
* Gebruikers kunnen zelf een licentie voor toepassingen en services.

### <a name="how-can-i-control-these-capabilities"></a>Hoe kan ik deze functies bepalen?
Een beheerder kan deze mogelijkheden met de volgende parameters van de Azure AD-cmdlet Set-MsolCompanySettings configureren:

* **AllowEmailVerifiedUsers** bepaalt of een gebruiker kunt maken of lid worden van een niet-beheerde adreslijst. Als u deze parameter op $false instelt, kunnen geen gebruikers via e-mail geverifieerde lid van de map.
* **AllowAdHocSubscriptions** Hiermee bepaalt u de mogelijkheid voor gebruikers om uit te voeren Self-serviceregistratie. Als u deze parameter op $false instelt, kunnen geen gebruikers Self-serviceregistratie uitvoeren. 
  
  > [!NOTE]
  > Flow en PowerApps proefversie mogelijkheid niet wordt beheerd door de **AllowAdHocSubscriptions** instelling. Raadpleeg voor meer informatie de volgende artikelen:
  > * [Hoe kan ik voorkomen dat mijn bestaande gebruikers Power BI gaan gebruiken?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Flow in uw organisatie Q & A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hoe de besturingselementen werken samen?
Deze twee parameters kunnen worden gebruikt in combinatie meer nauwkeurige controle over Self-serviceregistratie definiëren. Bijvoorbeeld, de volgende opdracht kunnen gebruikers uit te voeren Self-serviceregistratie, maar alleen als deze gebruikers al een account in Azure AD hebben (met andere woorden, gebruikers die een worden gemaakt via e-mail geverifieerde account moet eerst niet uitvoeren Self-serviceregistratie):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Het volgende stroomdiagram wordt uitgelegd voor de verschillende combinaties van deze parameters en de resulterende voorwaarden voor de directory en self-serviceregistratie.

![][1]

Zie voor meer informatie en voorbeelden van het gebruik van deze parameters [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Volgende stappen
* [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/directory-self-service-signup/SelfServiceSignUpControls.png
