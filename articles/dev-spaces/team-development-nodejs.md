---
title: Teamontwikkeling met Azure Dev Spaces met VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: b4c355c864f83bcd76c310fecb0f26dd3372e760
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162747"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamontwikkeling met Azure Dev Spaces

In deze zelfstudie leert u hoe u gelijktijdig kunt werken in verschillende ontwikkelomgevingen met behulp van meerdere ontwikkelruimten, om het werk gescheiden te houden in afzonderlijke ontwikkelruimten in hetzelfde cluster.

## <a name="call-a-service-running-in-a-separate-container"></a>Een service aanroepen die wordt uitgevoerd in een afzonderlijke container

In deze sectie maakt u een tweede service, `mywebapi`, en laat u deze aanroepen in `webfrontend`. Elke service wordt uitgevoerd in een afzonderlijke container. Vervolgens spoort u fouten op in beide containers.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Voorbeeldcode openen voor *mywebapi*
U beschikt al over voorbeeldcode voor `mywebapi` voor deze handleiding in een map met de naam `samples`. (Zo niet, dan gaat u naar https://github.com/Azure/dev-spaces en selecteert u **Clone or Download** om de GitHub-opslagplaats te downloaden.) De code voor deze sectie bevindt zich in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>*mywebapi* uitvoeren
1. Open de map `mywebapi` in een *afzonderlijk VS Code-venster*.
1. Open het **Opdrachtenpalet** (via het menu **Beeld | Opdrachtenpalet**), en gebruik automatisch aanvullen om te typen en deze opdracht te selecteren: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Deze opdracht moet niet worden verward met de opdracht `azds prep`, waarmee u het project configureert voor implementatie.
1. Druk op F5 en wacht tot de service is gebouwd en geïmplementeerd. Dit proces is voltooid, zodra de VS Code-balk voor foutopsporing wordt weergegeven.
1. Noteer de URL van het eindpunt. Deze ziet er ongeveer als volgt uit: http://localhost:\<portnumber\>. **Tip: op de VS Code-statusbalk wordt een klikbare URL weergegeven.** Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in de ontwikkelomgeving in Azure. Het localhost-adres is te zien omdat er nog geen openbare eindpunten zijn gedefinieerd in `mywebapi` en toegang daarom alleen mogelijk is binnen het Kubernetes-exemplaar. Voor uw gemak en om interactie met de privésessie mogelijk te maken vanaf de lokale computer wordt in Azure Dev Spaces een tijdelijke SSH-tunnel gemaakt naar de container die wordt uitgevoerd in Azure.
1. Wanneer `mywebapi` klaar is, opent u de browser naar het localhost-adres. U ziet nu een reactie van de `mywebapi`-service. (Hello from mywebapi).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Verzend een aanvraag van *webfrontend* naar *mywebapi*
Nu gaan we code schrijven in `webfrontend` waarmee een aanvraag wordt verzonden naar `mywebapi`.
1. Schakel over naar het VS Code-venster voor `webfrontend`.
1. Voeg deze coderegels toe boven aan `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Vervang* de code door de `/api`-GET-handler. Tijdens het verwerken van een aanvraag, wordt vervolgens een aanroep verzonden naar `mywebapi` en worden de resultaten van beide services geretourneerd.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

In het vorige codevoorbeeld wordt de `azds-route-as`-header van de binnenkomende aanvraag doorgestuurd naar de uitgaande aanvraag. Later ziet u hoe dit teamleden helpt om samen te ontwikkelen.

### <a name="debug-across-multiple-services"></a>Foutopsporing in meerdere services
1. Op dit punt moet `mywebapi` nog steeds worden uitgevoerd met het bijgevoegde foutopsporingsprogramma. Als dit niet het geval is, drukt u op F5 in het `mywebapi`-project.
1. Stel een onderbrekingspunt in de standaard-GET `/`-handler in.
1. Stel in het `webfrontend`-project een onderbrekingspunt in vlak voordat een GET-aanvraag wordt verzonden naar `http://mywebapi`.
1. Druk op F5 in het `webfrontend`-project.
1. Open de web-app en analyseer de code in beide services. Als het goed is, wordt nu een bericht weergegeven dat is samengesteld uit de twee services: Hello from webfrontend en Hello from mywebapi.

Dat is dus gelukt. U hebt nu een toepassing met meerdere containers waarin elke container afzonderlijk kan worden ontwikkeld en geïmplementeerd.

## <a name="learn-about-team-development"></a>Meer informatie over teamontwikkeling

[!INCLUDE [](includes/team-development-1.md)]

Zo ziet het er in de praktijk uit:
1. Ga naar het VS Code-venster voor `mywebapi` en wijzig de code van de standaard-GET `/`-handler, bijvoorbeeld:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](includes/team-development-2.md)]

[!INCLUDE [](includes/well-done.md)]

[!INCLUDE [](includes/clean-up.md)]




