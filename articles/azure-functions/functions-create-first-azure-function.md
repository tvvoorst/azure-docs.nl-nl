---
title: Uw eerste functie maken vanuit Azure Portal | Microsoft Docs
description: Leer hoe u uw eerste serverloze Azure-functie kunt maken met behulp van Azure Portal.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: d208a4b72a27eb288d46ee591f42a8f6b71c4f70
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094056"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Uw eerste functie maken in Azure Portal

Met Azure Functions kunt u uw code in een [serverloze](https://azure.microsoft.com/overview/serverless-computing/) omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing publiceren. In dit onderwerp leert u hoe met Azure Functions een 'Hallo wereld-functie' in Azure Portal kunt maken.

![Functie-app maken in Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> C#-ontwikkelaars dienen te overwegen [de eerste functie in Visual Studio 2017 te maken](functions-create-your-first-function-visual-studio.md) in plaats van in de portal. 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <http://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-function"></a>Een door HTTP geactiveerde functie maken

1. Vouw de nieuwe functie-app uit en klik vervolgens op de knop **+** naast **Functies**.

2.  Selecteer op de pagina **Ga snel aan de slag** de optie **WebHook + API**, **Kies een taal** voor uw functie en klik op **Deze functie maken**. 
   
    ![De Quick Start van Azure Functions in Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Een functie wordt gemaakt in de door u gekozen taal met de sjabloon voor een door HTTP geactiveerde functie. In dit onderwerp ziet u een C#-scriptfunctie in de portal, maar u kunt een functie maken in elke [ondersteunde taal](supported-languages.md). 

U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Klik in de nieuwe functie rechtsboven op **</> Functie-URL ophalen**, selecteer **Standaard (functietoets)** en klik vervolgens op **Kopiëren**. 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Plak de URL van de functie in de adresbalk van uw browser. Voeg waarde `&name=<yourname>` voor de querytekenreeks toe aan het einde van deze URL, en druk op de toets `Enter` op het toetsenbord om de aanvraag uit te voeren. U ziet het geretourneerde antwoord van de functie nu in de browser.  

    Hieronder volgt een voorbeeld van het antwoord in de Edge-browser (in andere browsers wordt mogelijk ook XML weergegeven):

    ![Het antwoord van de functie in de browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.   

3. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de traceringsuitvoer van de vorige uitvoering wilt zien, gaat u terug naar de functie in de portal en klikt u op de pijl onder aan het scherm om de **logboeken** uit te vouwen. 

   ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige door HTTP geactiveerde functie gemaakt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Zie [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (Azure Functions-HTTP- en webhookbindingen) voor meer informatie.



