---
title: Abonnementssleutels - Custom Speech Service ophalen
titlesuffix: Azure Cognitive Services
description: Informatie over het verkrijgen van abonnementssleutels voor aanroepen naar de Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: e4694928baf98bdb0d6aacead8dffec6bb73d6f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224756"
---
# <a name="obtain-subscription-keys"></a>Abonnementssleutels verkrijgen
Om te beginnen met behulp van de Azure Custom Speech Service, moet u eerst uw gebruikersaccount koppelen aan een Azure-abonnement. De lagen gratis en betaalde abonnementen zijn beschikbaar. Zie voor meer informatie over de lagen de [pagina met prijzen](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>De abonnementssleutel van een ophalen
1. U kunt een abonnementssleutel krijgen van de Azure-portal op twee manieren:

    * Ga naar de [Azure-portal](https://ms.portal.azure.com), en een nieuwe Cognitive Services-API toevoegen door te zoeken naar _Cognitive Services_ en vervolgens de optie **Cognitive Services API's**.

      ![Cognitive Services zoeken](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Of Ga rechtstreeks naar de [Cognitive Services API's](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Cognitive Services-API's](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Vul de volgende vereiste velden in:

      a. **Accountnaam**. Gebruik een naam die bij u past. Vergeet niet dat deze naam zodat u uw Cognitive Services-abonnement in de lijst met resources vinden kunt.

      b. **Abonnement**. Selecteer een van uw Azure-abonnementen.

      c. **API-type**. Selecteer **Custom Speech Service (Preview)**.

      d. **Locatie**. Het is momenteel **VS-West**.

      e. **Prijscategorie**. Selecteer de laag die bij u past. **F0** is de gratis laag. De quota die mogen worden gebruikt beschreven op de [pagina met prijzen](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Cognitive Services-account maken](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. U moet ofwel een weergave op uw dashboard of een service met de naam van het opgegeven vinden in uw lijst met resources. Wanneer u deze selecteert, ziet u een overzicht van uw service. In de lijst aan de linkerkant, onder **resourcebeheer**, selecteer **sleutels**. Kopie **sleutel 1**.

      De abonnementssleutel voor dit is vereist in de volgende stappen.

      ![SLEUTEL 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Kopieer niet de **abonnements-ID** van de overzichtspagina. U moet de abonnementssleutel in de volgende stap.
      >

      ![Overzicht abonnements-ID](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Selecteer uw gebruikersaccount om in te voeren van uw abonnementssleutel op het lint in de rechterbovenhoek. Selecteer op de vervolgkeuzelijst **abonnementen**.

      ![Menu-item voor abonnementen](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Een tabel met abonnementen weergegeven die leeg is de eerste keer die wordt geopend.

    ![Abonnementen-tabel](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Selecteer **nieuwe toevoegen**. Voer een naam voor het abonnement en de abonnementssleutel. Het kan zijn **sleutel 1** (primaire sleutel) of **sleutel 2** (secundaire sleutel) van uw abonnement.

      ![Sleutelnaam voor abonnement](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Als u wilt uploaden van gegevens, een model te trainen of een implementatie, moet u uw activiteiten Custom Speech Service koppelen aan een Azure-abonnement. Een gratis laag of een betaalde laag-abonnement kan het zijn. Zie de pagina [prijzen](https://www.microsoft.com/cognitive-services/en-us/pricing) voor meer informatie.

## <a name="get-a-subscription-id"></a>Een abonnements-ID ophalen
Als u een abonnements-ID, gaat u naar de Azure-portal. Zoeken naar *Cognitive Services* en *Custom Speech Service*, en volg de instructies.

> [!NOTE]
> De abonnementssleutel is vereist verderop in dit proces.
>

## <a name="next-steps"></a>Volgende stappen
* Beginnen met het maken van uw [aangepast akoestisch model](cognitive-services-custom-speech-create-acoustic-model.md).
* Beginnen met het maken van uw [aangepast taalmodel](cognitive-services-custom-speech-create-language-model.md).
