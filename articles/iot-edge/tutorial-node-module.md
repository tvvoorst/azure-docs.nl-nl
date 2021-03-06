---
title: Zelfstudie voor Azure IoT Edge Node.js | Microsoft Docs
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Node.js-code maakt en deze implementeert op een Edge-apparaat
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 016c221a2f864ecfd4bb96b076a32ed1ae639c6b
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422793"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Zelfstudie: een Node.js IoT Edge-module maken en implementeren op een gesimuleerd apparaat

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de zelfstudie Azure IoT Edge implementeren op een gesimuleerd apparaat in [Windows][lnk-tutorial1-win] of [Linux][lnk-tutorial1-lin]. In deze zelfstudie leert u het volgende:    

> [!div class="checklist"]
> * Visual Studio Code gebruiken om een IoT Edge Node.js-module te maken
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register 
> * De module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven


De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheidgegevens die worden gecommuniceerd naar en opgeslagen in de cloud te reduceren. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* U kunt uw ontwikkelcomputer of een virtuele machine gebruiken als een Edge-apparaat door de stappen te volgen in de snelstart voor [Linux-](quickstart-linux.md) of [Windows-apparaten](quickstart.md).

Cloudresources:

* Een gratis [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure. 

Ontwikkelingsresources:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) voor Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js en NPM](https://nodejs.org). Het NPM-pakket wordt gedistribueerd met Node.js. Dit betekent dat NMP automatisch wordt geïnstalleerd op de computer wanneer u Node.js downloadt.

## <a name="create-a-container-registry"></a>Een containerregister maken
In deze zelfstudie gebruikt u de Azure IoT Edge-extensie voor VS Code om een module te bouwen en maakt u een **containerinstallatiekopie** van de bestanden. Vervolgens pusht u deze installatiekopie naar een **register** waarin uw installatiekopieën worden opgeslagen en beheerd. Tot slot implementeert u de installatiekopie uit het register voor uitvoering op uw IoT Edge-apparaat.  

Voor deze zelfstudie kunt u elk register gebruiken dat compatibel is met Docker. Twee populaire Docker-registerservices die beschikbaar zijn in de cloud zijn [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) en [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In deze zelfstudie wordt Azure Container Registry gebruikt. 

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Containers** > **Azure Container Registry**.
2. Geef een naam op voor het register, kies een abonnement, kies een resourcegroep en stel de SKU in op **Basis**. 
3. Selecteer **Maken**.
4. Zodra het containerregister is gemaakt, gaat u er naartoe en selecteert u **Toegangssleutels**. 
5. Stel de **Gebruiker met beheerdersrechten** in op **Inschakelen**.
6. Kopieer de waarden voor **Aanmeldingsserver**, **Gebruikersnaam** en **wachtwoord**. U gebruikt deze waarden later in de zelfstudie. 

## <a name="create-an-iot-edge-module-project"></a>Een IoT Edge-moduleproject creëren
De volgende stappen laten zien hoe u een IoT-Edge Node.js-module maakt met behulp van Visual Studio Code en de Azure IoT Edge-extensie.

### <a name="create-a-new-solution"></a>Een nieuwe oplossing maken

Gebruik **NPM** om een Node.js-oplossingssjabloon te maken waarop u verder kunt bouwen. 

1. Selecteer in Visual Studio Code **View** > **Integrated Terminal** om de met VS code geïntegreerde terminal te openen.

2. Voer in de geïntegreerde terminal de volgende opdracht in om **yeoman** en de generator voor de Node.js Azure IoT Edge-module te installeren: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecteer **View** > **Command Palette** om het VS Code-opdrachtpalet te openen. 

3. Typ in het opdrachtpalet de opdracht **Azure: Sign in** en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u zich al hebt aangemeld, kunt u deze stap overslaan.

4. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken: 

   1. Selecteer de map waarin u de oplossing wilt maken. 
   2. Geef een naam op voor de oplossing of houd de standaardnaam **EdgeSolution** aan.
   3. Kies **Node.js-module** als de modulesjabloon. 
   4. Geef de module de naam **NodeModule**. 
   5. Geef het Azure-containerregister dat u in de vorige sectie hebt gemaakt, op als de opslagplaats voor installatiekopieën voor de eerste module. Vervang **localhost:5000** door de gekopieerde waarde voor de aanmeldingsserver. De uiteindelijke tekenreeks ziet er ongeveer als volgt uit: **\<registernaam\>.azurecr.io/nodemodule**.

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-node-module/repository.png)

In het VS Code-venster wordt de werkruimte van de IoT Edge-oplossing geladen. De werkruimte voor de oplossing bevat vijf onderdelen op het hoogste niveau. In deze zelfstudie gaan we niet de map **\.vscode** of het bestand **\.gitignore** bewerken. De map **modules** bevat de Node.js-code voor de module evenals Docker-bestanden voor het bouwen van uw module als een containerinstallatiekopie. In het bestand **\.env** worden uw referenties voor het containerregister opgeslagen. Het bestand **deployment.template.json** bevat de gegevens die de IoT Edge-runtime gebruikt om modules op een apparaat te implementeren. 

Als u geen containerregister hebt opgegeven bij het maken van uw oplossing, maar de standaardwaarde localhost:5000 hebt geaccepteerd, is er geen \.env-bestand gemaakt. 

   ![Werkruimte voor Node.js-oplossing](./media/tutorial-node-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor de containeropslagplaats opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. 

1. Open in VS Code Explorer het bestand **.env**. 
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister. 
3. Sla dit bestand op. 

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Elke sjabloon bevat voorbeeldcode. Met deze code worden gesimuleerde sensorgegevens uit de **tempSensor**-module gerouteerd naar IoT Hub. Voeg in deze sectie code toe om de berichten te analyseren met NodeModule voordat u ze verzendt. 

1. Open in VS Code Explorer **modules** > **NodeModule** > **app.js**.

5. Voeg een variabele voor de temperatuurdrempel toe onder de vereiste knooppuntmodules. De temperatuurdrempel bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Vervang de hele functie `PipeMessage` door de functie `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Vervang in functie `client.on()` de functienaam `pipeMessage` door `filterMessage`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Kopieer het volgende codefragment in de functieaanroep `client.open()`, na `client.on()` binnen de `else`-instructie. Deze functie wordt aangeroepen wanneer de gewenste eigenschappen zijn bijgewerkt.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Sla dit bestand op.

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de NodeModule waarmee berichten worden gefilterd waarin de gemelde temperatuur van de computer onder de aanvaardbare drempelwaarde komt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen. 

1. Meld u aan bij Docker door de volgende opdracht in te voeren in de geïntegreerde terminal van Visual Studio Code, zodat u de module-installatiekopie naar het ACR kunt pushen: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Gebruik de gebruikersnaam, het wachtwoord en de aanmeldingsserver die u in de eerste sectie hebt gekopieerd uit het Azure-containerregister. Of haal deze opnieuw op in de sectie **Toegangssleutels** van het register in Azure Portal.

2. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing. 

   Dit bestand zorgt ervoor dat deze twee modules worden geïmplementeerd in `$edgeAgent`: **tempSensor** (waarmee apparaatgegevens worden gesimuleerd) en **NodeModule**. De waarde `NodeModule.image` is ingesteld op een installatiekopie met de versie Linux amd64. Zie [Informatie over het gebruiken, configureren en hergebruiken van IoT Edge-modules](module-composition.md) voor meer informatie over distributiemanifesten.

   Dit bestand bevat ook uw registerreferenties. Uw gebruikersnaam en wachtwoord in het sjabloonbestand zijn tijdelijke aanduidingen. Wanneer u het distributiemanifest genereert, worden de velden bijgewerkt met de waarden die u hebt toegevoegd aan **.env**. 

4. Voeg de moduledubbel NodeModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`: 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Sla dit bestand op.
6. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**. 

Wanneer u Visual Studio Code de opdracht geeft om uw oplossing te bouwen, wordt eerst een `deployment.json`-bestand gemaakt in een nieuwe **configuratiemap** op basis van de informatie in het distributiesjabloon. Vervolgens worden twee opdrachten uitgevoerd in de geïntegreerde terminal: `docker build` en `docker push`. Met deze twee opdrachten wordt uw code gebouwd, de Node.js-code opgeslagen in een container, en vervolgens naar het containerregister gepusht dat u hebt opgegeven toen u de oplossing initialiseerde. 

U kunt het volledige adres van de containerinstallatiekopie, inclusief de tag, zien in de opdracht `docker build` die wordt uitgevoerd in de geïntegreerde terminal van VS Code. Het adres van de installatiekopie is opgebouwd uit informatie uit het `module.json`-bestand, in de indeling **\<opslagplaats\>:\<versie\>-\<platform\>**. Voor deze zelfstudie ziet dit adres er ongeveer als volgt uit: **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>De oplossing implementeren en uitvoeren

In dit snelstartartikel voor het instellen van uw IoT Edge-apparaat hebt u een module geïmplementeerd met behulp van de Azure-portal. U kunt modules ook implementeren via de Azure IoT Toolkit-extensie voor Visual Studio Code. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.json**. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

1. Voer in het opdrachtenpalet van VS Code de opdracht **Azure IoT Hub: Select IoT Hub** uit. 

2. Kies het abonnement en de IoT-hub met het IoT Edge-apparaat dat u wilt configureren. 

3. Vouw in VS Code Explorer de sectie **Azure IoT Hub Devices** uit. 

4. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**. 

   ![Implementatie voor één apparaat maken](./media/tutorial-node-module/create-deployment.png)

5. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json. 

6. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe **NodeModule** wordt uitgevoerd, samen met de module **TempSensor** en de **$edgeAgent** en **$edgeHub**. 


## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

1. Klik op **...** en selecteer **Controle D2C-berichten starten** om de gegevens te controleren die binnenkomen bij de IoT-hub.
2. Als u de D2C-berichten voor een specifiek apparaat wilt controleren, klikt u met de rechtermuisknop op dit apparaat in de lijst en selecteert u **Controle D2C-berichten starten**.
3. Voer de opdracht **Azure IoT Hub: Stop monitoring D2C message** uit in het opdrachtpalet om het controleren van berichten te stoppen. 
4. Als u de moduledubbel wilt weergeven of bewerken, klikt u met de rechtermuisknop op deze moduledubbel in de lijst en selecteert u **Moduledubbel bewerken**. Als u de moduledubbel wilt bijwerken, slaat u het dubbele JSON-bestand op, klikt u met de rechtermuisknop in de editor en selecteert u **Moduledubbel bijwerken**.
5. Als u Docker-logboeken wilt bekijken, kunt u [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) voor VS Code installeren. U kunt de actieve modules lokaal zoeken in Docker Explorer. Klik in het contextmenu op **Logboeken weergeven** om ze te bekijken in de geïntegreerde terminal. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. U kunt doorgaan met en van de volgende zelfstudies om te leren waarmee Azure IoT Edge u nog meer kan helpen uw gegevens om te zetten in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Azure-functies als module implementeren](tutorial-deploy-function.md)
> [Azure Stream Analytics als module implementeren](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
