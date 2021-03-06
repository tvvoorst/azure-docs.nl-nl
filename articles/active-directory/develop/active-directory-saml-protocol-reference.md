---
title: Hoe Azure AD het SAML-protocol gebruikt | Microsoft Docs
description: Dit artikel bevat een overzicht van de profielen Single Sign-On en eenmalige afmelding SAML in Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b9ae7cf9d6fd383c7cdfa3957b5d9b94c7207d50
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900652"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hoe Azure AD het SAML-protocol gebruikt

Maakt gebruik van het SAML 2.0-protocol van de Azure Active Directory (Azure AD) om in te schakelen van toepassingen voor een eenmalige aanmelding voor hun gebruikers. De [Single Sign-On](single-sign-on-saml-protocol.md) en [eenmalige afmelding](single-sign-out-saml-protocol.md) SAML-profielen van Azure AD wordt uitgelegd hoe het SAML-asserties ondertekend, protocollen en -bindingen worden gebruikt in de id-provider-service.

SAML-Protocol is vereist voor de id-provider (Azure AD) en de serviceprovider (de toepassing) voor het uitwisselen van gegevens over zichzelf.

Wanneer een toepassing wordt geregistreerd bij Azure AD, wordt de app-ontwikkelaar federation-gerelateerde informatie geregistreerd bij Azure AD. Deze informatie omvat de **omleidings-URI** en **Metadata URI** van de toepassing.

Azure AD maakt gebruik van de cloudservice **Metadata URI** om op te halen van de ondertekeningssleutel en de URI van de afmelding. Als de toepassing biedt geen ondersteuning voor een metagegevens URI, de ontwikkelaar moet Neem contact op met Microsoft ondersteuning voor de afmelding URI en de handtekeningsleutel.

Azure Active Directory wordt aangegeven dat tenant-specifieke en algemene (tenant-onafhankelijk) single sign-on en één afmelding eindpunten. Deze URL's adresseerbaar locaties vertegenwoordigen--ze zijn niet alleen id's, zodat u kunt gaan met het eindpunt te lezen van de metagegevens.

* Het eindpunt van de tenant-specifieke bevindt zich in `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. De *<TenantDomainName>* aanduiding een geregistreerde domeinnaam of TenantID-GUID van een Azure AD-tenant. De federatiemetagegevens van de tenant contoso.com is bijvoorbeeld op: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Het eindpunt van de tenant-onafhankelijke bevindt zich in `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. In deze eindpuntadres **algemene** wordt weergegeven in plaats van een domeinnaam van tenant- of -ID.

Zie voor meer informatie over de federatieve metagegevens documenten die Azure AD publiceert [Federatiemetagegevens](azure-ad-federation-metadata.md).
