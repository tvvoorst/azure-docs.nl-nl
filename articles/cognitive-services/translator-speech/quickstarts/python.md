---
title: 'Snelstart: Translator Speech-API met Python'
titlesuffix: Azure Cognitive Services
description: Ontdek informatie en codevoorbeelden om u te helpen snel aan de slag te gaan met de Translator Speech-API.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 601e8473482cf6d90151b221309aef4d48d368d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979012"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Snelstart: Translator Speech-API met Python
<a name="HOLTop"></a>

In dit artikel leest u hoe de Translator Speech-API gebruikt voor het vertalen van gesproken woorden in een WAV-bestand.

## <a name="prerequisites"></a>Vereisten

U hebt [Python 3.x](https://www.python.org/downloads/) nodig om deze code uit te voeren.

U moet het [websocket-clientpakket](https://pypi.python.org/pypi/websocket-client) voor Python installeren.

In de map met het uitvoerbare bestand dat u hebt gemaakt op basis van de volgende code moet ook een WAV-bestand staan met de naam speak.wav. Dit WAV-bestand moet de standaardindeling hebben: PCM, 16-bits, 16 kHz, mono. 

U moet beschikken over een [account voor de Cognitive Services-API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) en de **Microsoft Translator Speech-API**. U hebt een betaalde abonnementssleutel nodig op het [Azure-dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Spraak vertalen

Met de volgende code wordt spraak van de ene naar de andere taal vertaald.

1. Maak een nieuw Python-project in uw favoriete IDE.
2. Voeg de onderstaande code toe.
3. Vervang de waarde `key` door een geldige toegangssleutel voor uw abonnement.
4. Voer het programma uit.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**Gesproken antwoord vertalen**

Deze handeling is geslaagd als het bestand 'speak2.wav' is gemaakt. Het bestand bevat de vertaling van de woorden die te horen zijn in 'speak.wav'.

[Terug naar boven](#HOLTop)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Translator Speech-zelfstudie](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Zie ook 

[Translator Speech-overzicht](../overview.md)
[API-naslagmateriaal](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
