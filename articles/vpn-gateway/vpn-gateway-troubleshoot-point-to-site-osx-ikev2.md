---
title: Problemen oplossen met Azure Point-to-Site VPN-verbindingen van clients voor Mac OS X | Microsoft Docs
description: Stappen voor het oplossen van P2S Mac OS X-VPN-clientverbindingen
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: bc8e95b07d984505fc0e5fdad41fe120463b5b0e
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585522"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Problemen met punt-naar-Site VPN-verbindingen van Mac OS X-VPN-clients oplossen

Dit artikel helpt bij het oplossen van problemen met punt-naar-Site de netwerkverbinding van Mac OS X met behulp van de systeemeigen VPN-client en IKEv2. De VPN-client in Mac voor IKEv2 is zeer eenvoudig en is niet toegestaan voor veel aanpassing. Er zijn slechts vier instellingen die moeten worden gecontroleerd:

* Serveradres
* Externe ID
* Lokale ID
* Verificatie-instellingen
* Versie van het besturingssysteem (10.11 of hoger)


## <a name="VPNClient"></a> Verificatie op basis van certificaten oplossen
1. Controleer de instellingen van de VPN-client. Ga naar de **netwerkinstelling** door te drukken opdracht + Shift en typ vervolgens 'VPN' om te controleren of de instellingen voor de VPN-client. In de lijst, klikt u op de VPN-vermelding die moet worden onderzocht.

  ![IKEv2-certificaat gebaseerde verificatie](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Controleer de **serveradres** is de volledige FQDN en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het adres van de Server (FQDN-Gateway).
4. De **lokale ID** moet gelijk zijn aan de **onderwerp** van het clientcertificaat.
5. Klik op **verificatie-instellingen** om de pagina verificatie-instellingen te openen.

  ![Verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Controleer **certificaat** is geselecteerd in de vervolgkeuzelijst.
7. Klik op de **Selecteer** knop en controleer of het juiste certificaat is geselecteerd. Klik op **OK** eventuele wijzigingen op te slaan.

## <a name="ikev2"></a>Verificatie via gebruikersnaam en wachtwoord oplossen

1. Controleer de instellingen van de VPN-client. Ga naar de **netwerkinstelling** door te drukken opdracht + Shift en typ vervolgens 'VPN' om te controleren of de instellingen voor de VPN-client. In de lijst, klikt u op de VPN-vermelding die moet worden onderzocht.

  ![Wachtwoord voor IKEv2-gebruikersnaam](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Controleer de **serveradres** is de volledige FQDN en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het adres van de Server (FQDN-Gateway).
4. De **lokale ID** mag leeg zijn.
5. Klik op de **Authentication-instelling** knop en controleer of dat 'Username' is geselecteerd in de vervolgkeuzelijst.

  ![Verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Controleer of de juiste referenties zijn ingevoerd.

## <a name="additional"></a>Extra stappen

Als u de vorige stappen en alles juist is geconfigureerd, downloadt u [Wireshark](https://www.wireshark.org/#download) en een pakketopname uit te voeren.

1. Filteren op *isakmp* en bekijk de **IKE_SA** pakketten. U zou het mogelijk om te kijken naar de details van het voorstel SA onder de **nettolading: Security Association**. 
2. Controleer of de client en de server een gemeenschappelijke set hebben.

  ![pakket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie, [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
