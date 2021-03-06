---
title: Aanmelding bij Azure Active Directory-accounts instellen met een ingebouwde beleid in Azure Active Directory B2C | Microsoft Docs
description: Aanmelding bij Azure Active Directory-accounts instellen een ingebouwd beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f51fbff11412324ad167d49202f7215cefb5ac2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076915"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Aanmelding bij Azure Active Directory-accounts instellen met een ingebouwde beleid in Azure Active Directory B2C

>[!NOTE]
> Deze functie is beschikbaar als openbare preview. Gebruik de functie niet in een productieomgeving.

Dit artikel ziet u hoe u aanmelding voor gebruikers van een specifieke Azure Active Directory (Azure AD)-organisatie met behulp van een ingebouwde beleid in Azure Active Directory (Azure AD) B2C inschakelen.

## <a name="create-an-azure-ad-app"></a>Een Azure AD-app maken

Om in te schakelen aanmelding voor gebruikers van een specifieke Azure AD-organisatie, moet u een toepassing registreren met de organisatie Azure AD-tenant.

>[!NOTE]
>`Contoso.com` wordt gebruikt voor de organisatie Azure AD-tenant en `fabrikamb2c.onmicrosoft.com` wordt gebruikt als de Azure AD B2C-tenant in de volgende instructies.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-tenant (contoso.com) door te klikken op de map- en abonnementsfilter in het bovenste menu en de map met uw Azure AD-tenant te kiezen.
3. Kies **alle services** in de linkerbovenhoek van de Azure portal en vervolgens zoeken naar en selecteer **App-registraties**.
4. Selecteer **Nieuwe toepassing registreren**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
6. Voor de **toepassingstype**, selecteer `Web app / API`.
7. Voor de **aanmeldings-URL**, voer de volgende URL in kleine letters, waarbij `your-tenant` wordt vervangen door de naam van uw Azure AD B2C-tenant (fabrikamb2c.onmicrosoft.com):

    ```
    https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

    Alle URL's moeten nu gebruikmaken van [b2clogin.com](b2clogin.md).

8. Klik op **Create**. Kopieer de **toepassings-ID** moet later worden gebruikt.
9. Selecteer de toepassing en selecteer vervolgens **instellingen**.
10. Selecteer **sleutels**, geef de beschrijving van de sleutel, selecteer een tijdsduur en klik vervolgens op **opslaan**. Kopieer de waarde van de sleutel die wordt weergegeven voor later gebruik.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Azure AD als id-provider in uw tenant configureren

1. Zorg ervoor dat u de map met Azure AD B2C-tenant (fabrikamb2c.onmicrosoft.com) door te klikken op de **map- en abonnementsfilter** in het bovenste menu en het kiezen van de map met uw Azure AD B2C tenant.
2. Kies **alle services** in de linkerbovenhoek van de Azure portal en vervolgens zoeken naar en selecteer **Azure AD B2C**.
3. Selecteer **id-providers**, en selecteer vervolgens **toevoegen**.
4. Voer een **naam**. Voer bijvoorbeeld 'Contoso Azure AD'.
5. Selecteer **type id-provider**, selecteer **Open ID Connect (Preview)**, en klik vervolgens op **OK**.
6. Klik op **deze id-provider instellen**
7. Voor **metagegevens-url**, voer de volgende URL vervangt `your-tenant` met de naam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```
8. Voor **Client-id**, voer de toepassings-ID die u eerder hebt genoteerd en voor **clientgeheim**, voer de waarde van de sleutel die u eerder hebt genoteerd.
9. Geef eventueel een waarde voor **Domain_hint** (bijvoorbeeld `ContosoAD`). Dit is de waarde moet worden gebruikt als verwijzingen naar deze id-provider met behulp van *domain_hint* in de aanvraag. 
10. Klik op **OK**.
11. Selecteer **deze id-provider claims toewijzen** en stel de volgende claims:
    
    - Voor **gebruikers-ID**, voer `oid`.
    - Voor **weergavenaam**, voer `name`.
    - Voor **voornaam**, voer `given_name`.
    - Voor **achternaam**, voer `family_name`.
    - Voor **e**, voer `unique_name`.

12. Klik op **OK**, en klik vervolgens op **maken** aan uw configuratie op te slaan.
