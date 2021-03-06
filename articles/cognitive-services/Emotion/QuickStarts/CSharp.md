---
title: Emotion -API C# snel aan de slag | Microsoft Docs
description: Ophalen van informatie en een voorbeeld van code kunt u snel aan de slag met behulp van de Emotion-API met C# in cognitieve Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016562"
---
# <a name="emotion-api-c-quick-start"></a>Emotion -API C# snel starten

> [!IMPORTANT]
> De Video API Preview is op 30 oktober 2017 beëindigd. Als u wilt extraheren eenvoudig inzichten van video's, probeert de nieuwe [Video indexeerfunctie API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Ook kunt u deze ervaringen van inhoud, zoals de lijst met zoekresultaten verbeteren doordat gesproken woorden, vlakken tekens en emoties. Zie voor meer informatie, de [Video indexeerfunctie Preview](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) overzicht.

Dit artikel vindt u informatie en een voorbeeld van code kunt u snel aan de slag met behulp van de [Emotion-API herkennen methode](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) met C#. U kunt deze gebruiken voor het herkennen van de emoties uitgedrukt in een of meer personen in een installatiekopie van. 

## <a name="prerequisites"></a>Vereisten
* Ophalen van de cognitieve Services [Emotion-API-vensters SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Vraag uw gratis [abonnementssleutel](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Emotion erkenning C#-voorbeeldaanvraag

Een nieuwe Console-oplossing in Visual Studio maakt en vervolgens Program.cs vervangen door de volgende code. Wijzig de `string uri` gebruiken de regio waar u de sleutels van uw abonnement hebt verkregen. Vervang de **Ocp-Apim-Subscription-Key** waarde met de sleutel geldig abonnement. De abonnementssleutel vindt u bij de Azure portal. In het navigatievenster aan de linkerkant onder de **sleutels** sectie, blader naar de resource Emotion-API. Op deze manier kunt u de juiste krijgen verbinding URI in de **overzicht** Configuratiescherm voor uw resource vermeld onder **eindpunt**.

![Uw API-sleutels voor resource](../../media/emotion-api/keys.png)

U kunt een bibliotheek zoals de reactie van uw aanvraag verwerken `Newtonsoft.Json`. Als een reeks beheerbare objecten aangeroepen Tokens kunt u een JSON-tekenreeks verwerken. Deze bibliotheek toevoegen aan uw pakket, met de rechtermuisknop op het project in Solution Explorer en selecteer **Nuget-pakketten beheren**. Zoek vervolgens naar **Newtonsoft**. Het eerste resultaat moet **Newtonsoft.Json**. Selecteer **Installeren**. U kunt nu verwijzen naar deze bibliotheek in uw toepassing.

![Newtonsoft.Json installeren](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Herkent emoties steekproef antwoord
Een geslaagde aanroep retourneert een matrix met face-vermeldingen en hun bijbehorende emotion-scores. Ze zijn gerangschikt op face rechthoek grootte in aflopende volgorde. Een leeg antwoord geeft aan dat er geen vlakken zijn gedetecteerd. Een emotion-vermelding bevat de volgende velden:

* faceRectangle: locatie van de rechthoek van gezicht in de afbeelding
* scores: Emotion scores voor elk vlak in de afbeelding 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
