---
title: IoT DevKit naar de cloud--IoT DevKit AZ3166 verbindt met Azure IoT Hub | Microsoft Docs
description: In deze zelfstudie leert u hoe u instelt en verbindt IoT DevKit AZ3166 met Azure IoT Hub, zodat het verzenden van gegevens naar de Azure-cloud-platform.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 55901d6f3bcbf5511b6921939fdcba03972efed3
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182838"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 verbinden met Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

U kunt de [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) te ontwikkelen en prototype Internet of Things (IoT)-oplossingen die van Microsoft Azure-services profiteren. Het bevat een bord Arduino-compatibel met uitgebreide randapparatuur en sensoren, een open-source-bord-pakket en een groeiend [projecten catalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Wat u allemaal doen

Verbinding maken met de [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) naar een Azure-IoT-hub die u maakt, de temperatuur en vochtigheid gegevens van sensoren te verzamelen en verzenden van de gegevens naar de IoT-hub.

Heb je nog een DevKit? Probeer de [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) of [aanschaffen van een DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Wat u leert

* Hoe u de IoT DevKit verbinden met een draadloos toegangspunt en uw ontwikkelomgeving voorbereiden.
* Over het maken van een IoT-hub en een apparaat registreren voor de MXChip IoT DevKit.
* Klik hier voor meer informatie over het verzamelen van sensorgegevens door het uitvoeren van een voorbeeld van toepassing op de MXChip IoT DevKit.
* Klik hier voor meer informatie over het verzenden van de sensorgegevens naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een bord MXChip IoT DevKit met een Micro-USB-kabel. [Nu downloaden](https://aka.ms/iot-devkit-purchase).
* Een computer met Windows 10- of macOS 10.10 +.
* Een actief Azure-abonnement. [Activeren van een gratis 30-daagse proefversie Microsoft Azure-account](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Bereid uw hardware

De volgende hardware op de computer koppelen:

* DevKit bord
* Micro-USB-kabel

![Vereiste hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Als u wilt de DevKit verbinden met uw computer, de volgende stappen uit:

1. Het einde van de USB-verbinding naar uw computer.

2. Het einde Micro-USB verbinden met de DevKit.

3. De groene LED voor energiebeheer wordt bevestigd dat de verbinding.

   ![Hardwareverbindingen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi configureren

IoT-projecten zijn afhankelijk van verbinding met internet. Gebruik de volgende instructies voor het configureren van de DevKit verbinding maken met Wi-Fi.

### <a name="enter-ap-mode"></a>Azië en Stille Oceaan-modus

Houd ingedrukt B, push en release van de knop opnieuw instellen en loslaat B. Uw DevKit in Azië en Stille Oceaan-modus geplaatst voor het configureren van Wi-Fi. Het scherm wordt weergegeven de serviceset-id (SSID) van de DevKit en de IP-adres van de configuratie-portal.

![Knop, knop B en SSID opnieuw instellen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Verbinding maken met DevKit Azië en Stille Oceaan

Gebruik een ander apparaat Wi-Fi ingeschakeld (computer of mobiele telefoon) nu verbinding maken met de DevKit SSID (gemarkeerd in de vorige afbeelding). Het wachtwoord leeg laten.

![Informatie over het netwerk en de knop verbinding maken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Wi-Fi configureren voor de DevKit

Het IP-adres wordt weergegeven op het scherm DevKit op uw computer of mobiele telefoon browser openen, selecteert u het Wi-Fi-netwerk dat u de DevKit wilt verbinden met en typ het wachtwoord. Selecteer **Verbinden**.

![Wachtwoord en de knop verbinding maken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Wanneer de verbinding slaagt, wordt de DevKit opnieuw wordt opgestart in een paar seconden. Vervolgens ziet u de naam Wi-Fi- en IP-adres op het scherm:

![Naam Wi-Fi- en IP-adres](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Het IP-adres weergegeven in de foto mogelijk niet overeen met het IP-adres toegewezen en wordt weergegeven op het scherm DevKit. Dit is normaal, omdat het Wi-Fi DHCP gebruikt voor het IP-adressen dynamisch toewijzen.

Nadat Wi-Fi is geconfigureerd, wordt uw referenties op het apparaat voor die verbinding behouden, zelfs als het apparaat losgekoppeld wordt. Bijvoorbeeld, als u de DevKit voor Wi-Fi op uw startpagina configureren en vervolgens de DevKit duren voordat het kantoor, moet u Azië en Stille Oceaan-modus (te beginnen bij de stap in de sectie 'Voer Azië en Stille Oceaan modus') als u wilt de DevKit verbinden met uw kantoor Wi-Fi configureren. 

## <a name="start-using-the-devkit"></a>Start met behulp van de DevKit

De standaard-app die wordt uitgevoerd op de DevKit controleert of de nieuwste versie van de firmware en sommige sensorgegevens diagnose wordt voor u.

### <a name="upgrade-to-the-latest-firmware"></a>Een upgrade uitvoert naar de meest recente firmware

> [!NOTE]
> Sinds v1.1 kan DevKit ST-SAFE in bootloader. U moet de firmware als u een eerdere versie dan v1.1 uitvoert.

Als u een upgrade nodig, ziet het scherm u de huidige en de meest recente firmware-versies. Als u wilt bijwerken, gaat u als volgt de [firmware bijwerken](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) handleiding.

![Weergave van de huidige en de meest recente firmware-versies](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Dit is een eenmalige taak. Nadat u een begin met ontwikkelen in de DevKit en uw app downloaden, komen de meest recente firmware met uw app.

### <a name="test-various-sensors"></a>Testen van verschillende sensoren

Druk op de knop B voor het testen van de sensoren. Indrukken en loslaten van de knop B om te bladeren door elke sensor blijven.

![Knop B en sensor weergeven](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

### <a name="install-azure-iot-workbench"></a>Azure IoT-Workbench installeren

Het is raadzaam [Azure IoT Workbench](https://aka.ms/iot-workbench) -extensie voor Visual Studio Code ontwikkelen op de DevKit.

Azure IoT-Workbench biedt een geïntegreerde ervaring voor het ontwikkelen van IoT-oplossingen. Zo kunt beide op apparaat en de cloud ontwikkelen met behulp van Azure IoT en andere services. U kunt deze Channel 9 video als u wilt dat een overzicht van wat het doet.

Volg deze stappen voor de ontwikkelomgeving voorbereiden voor DevKit:

1. Download en installeer [Arduino IDE](https://www.arduino.cc/en/Main/Software). Het biedt de benodigde hulpprogrammaketen voor compileren en Arduino code uploaden.
    * **Windows**: gebruik Windows Installer-versie.
    * **macOS**: slepen en neerzetten het uitgepakte **Arduino.app** in `/Applications` map.
    * **Ubuntu**: zoals in de map uitpakken `$HOME/Downloads/arduino-1.8.5`

2. Installeer [Visual Studio Code](https://code.visualstudio.com/), een platformoverschrijdende broncode-editor met krachtige developer-hulpmiddelen, zoals IntelliSense code uitvoeren en fouten opsporen.

3. Zoek naar **Azure IoT Workbench** in de marketplace extensie en installeer deze.
    ![Installeer Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) samen met de IoT-Workbench, andere afhankelijke uitbreidingen worden geïnstalleerd.

4. Open **bestand > Voorkeuren > instellingen** en voeg de volgende regels Arduino configureren.
    * **Windows**:

    ```json
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

    * **macOS**:

    ```json
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

    * **Ubuntu**:

    ```json
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

5. Klik op `F1` openen het opdrachtenpalet, type en selecteer **Arduino: bord Manager**. Zoeken naar **AZ3166** en installeer de nieuwste versie.
    ![DevKit SDK installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>ST-koppeling stuurprogramma's installeren

[ST-koppeling/V2](http://www.st.com/en/development-tools/st-link-v2.html) is de USB-interface die IoT DevKit gebruikt om te communiceren met uw ontwikkelcomputer. De specifieke stappen voor de machine toegang tot uw apparaat.

* **Windows**: downloaden en installeren van USB-stuurprogramma van [STMicroelectronics website](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Er zijn geen stuurprogramma is vereist voor macOS.
* **Ubuntu**: Voer de volgende in de terminal en meld u af en meld u aan voor de groepswijziging door te voeren:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu zijn u klaar met het voorbereiden en configureren uw ontwikkelomgeving. Het 'Hallo wereld'-voorbeeld laat het ons maken voor IoT: temperatuur telemetrische gegevens verzenden naar Azure IoT Hub.

## <a name="build-your-first-project"></a>Bouw uw eerste project

1. Zorg ervoor dat uw IoT DevKit is **niet verbonden** op uw computer. VS Code eerst start, en vervolgens de DevKit verbinden met uw computer.

1. Schakel in de juiste status onderste balk de **MXCHIP AZ3166** wordt weergegeven als de geselecteerde bord en seriële poort met **STMicroelectronics** wordt gebruikt.
    ![Bord- en COM selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Klik op `F1` openen het opdrachtenpalet, type en selecteer **IoT Workbench: voorbeelden**. Selecteer vervolgens **IoT DevKit** als bord.

1. Zoek in de pagina IoT Workbench voorbeelden **aan de slag** en klikt u op **Open voorbeeld**. Vervolgens selecteert het standaardpad voor het downloaden van de voorbeeldcode.
    ![Open voorbeeld](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Als u geen Arduino-extensie in VS-Code die zijn geïnstalleerd, klikt u op de **installeren** in het deelvenster meldingen.
    ![Arduino-extensie installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. Klik in het nieuwe venster geopende project op `F1` openen het opdrachtenpalet, type en selecteer **IoT Workbench: Cloud**en selecteer vervolgens **Azure inrichten**. Volg de stapsgewijze handleiding voor het voltooien van de inrichting van uw Azure-IoT-Hub en het maken van het apparaat.
    ![Cloud inrichten](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Klik op `F1` openen het opdrachtenpalet, type en selecteer **IoT Workbench: apparaat**en selecteer vervolgens **configuratie-instellingen voor apparaten > Selecteer IoT Hub apparaat Connection String**.

1. Op DevKit, houdt u **een knop**, push en laat de **opnieuw** knop en loslaten **een knop**. Uw DevKit krijgt de configuratiemodus en slaat de verbindingsreeks.
    ![Verbindingsreeks](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klik op `F1` Typ wederom en selecteer **IoT Workbench: apparaat**en selecteer vervolgens **apparaat uploaden**.
    ![Arduino uploaden](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

De DevKit opnieuw wordt opgestart en de code wordt gestart.

> [!NOTE]
> Als er fouten of onderbrekingen, kunt u altijd herstellen door de opdracht nogmaals uit te voeren.

## <a name="test-the-project"></a>Het project testen

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>De telemetrie die verzonden naar Azure IoT Hub bekijken

Klik op het pictogram power plug op de statusbalk te openen van de seriële Monitor: ![seriële monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

De voorbeeldtoepassing wordt uitgevoerd wanneer u de volgende resultaten ziet:

* De seriële Monitor geeft het bericht verzonden naar de IoT-Hub.
* De LED op de MXChip IoT DevKit knippert.

![Seriële monitor-uitvoer](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>De telemetrie ontvangen via Azure IoT Hub bekijken

U kunt [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) voor het bewaken van apparaat-naar-cloud (D2C) berichten in IoT Hub.

1. Zoek in Visual Studio Code, **Azure IoT Toolkit** in de marketplace extensie en installeer deze.

1. Meld u [Azure-portal](https://portal.azure.com/), vinden de IoT-Hub die u hebt gemaakt.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. In de **beleid voor gedeelde toegang** deelvenster, klikt u op de **iothubowner beleid**, en noteer de verbindingsreeks van uw IoT-hub.
    ![Azure IoT Hub-verbindingsreeks](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Vouw in Visual Studio Code, **AZURE IOT HUB-apparaten** Klik in de linkerbenedenhoek op **IoT Hub-verbindingsreeks ingesteld**.
    ![Azure IoT Hub-verbindingsreeks instellen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Klik op **IoT: Start monitoring D2C message** in het contextmenu.

1. In **uitvoer** deelvenster ziet u de binnenkomende D2C-berichten naar de IoT Hub.
    ![D2C message](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, kunt u controleren voor een oplossing in de [IoT DevKit Veelgestelde vragen over](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of contact opnemen met ons [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). U kunt ons ook feedback geven door een opmerking op deze pagina verlaten.

## <a name="next-steps"></a>Volgende stappen

U hebt een MXChip IoT DevKit is verbonden met uw IoT-hub en u hebt de vastgelegde gegevens verzonden naar uw IoT-hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
