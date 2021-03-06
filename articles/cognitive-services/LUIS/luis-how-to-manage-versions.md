---
title: Versies in LUIS-apps beheren
titleSuffix: Azure Cognitive Services
description: Versies kunnen u bouwt en publiceert verschillende modellen. Er is een goede gewoonte om te klonen van de huidige actieve model naar een andere versie van de app voordat u wijzigingen in het model.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033784"
---
# <a name="manage-versions"></a>Versies beheren

Versies kunnen u bouwt en publiceert verschillende modellen. Er is een goede gewoonte om te klonen van de huidige actieve model met een andere [versie](luis-concept-version.md) van de app voordat u wijzigingen in het model. 

Als u wilt werken met versies, opent u uw app door het selecteren van de naam op **mijn Apps** pagina en selecteer vervolgens **beheren** in de bovenste balk, schakelt u vervolgens **versies** in het linkernavigatievenster. 

De lijst met versies laten zien welke versies worden gepubliceerd, waarin ze worden gepubliceerd, en welke versie momenteel actief is. 

[![](./media/luis-how-to-manage-versions/versions-import.png "Sectie, pagina-versies beheren")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Een versie klonen

1. Selecteer de versie die u wilt klonen Selecteer **kloon** via de werkbalk. 

2. In de **kloon versie** in het dialoogvenster, typ een naam voor de nieuwe versie, zoals '0.2'.

   ![Dialoogvenster van de kloon-versie](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versie-ID kan bestaan alleen uit tekens, cijfers of '.' en mag niet langer zijn dan 10 tekens.
 
 Een nieuwe versie met de opgegeven naam gemaakt en ingesteld als de actieve versie.

## <a name="set-active-version"></a>Actieve versie instellen

Selecteer een versie in de lijst en selecteer vervolgens **Maak actief** via de werkbalk. 

[![](./media/luis-how-to-manage-versions/versions-other.png "Sectie, pagina-versies beheren")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importversie

1. Selecteer **importeren versie** via de werkbalk. 

2. In de **de nieuwe versie importeren** pop-upvenster, geef de nieuwe naam voor de versie van tien tekens. U hoeft in te stellen van een versie-ID als de versie in het JSON-bestand al in de app bestaat.

    ![Sectie, pagina-versies beheren](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Nadat u een versie importeert, wordt de nieuwe versie de actieve versie.

<a name = "export-version"></a>

## <a name="other-actions"></a>Andere acties

* Naar **verwijderen** een versie gebruikt, selecteert u een versie in de lijst en vervolgens **verwijderen** via de werkbalk. Selecteer **OK**. 
* Naar **naam** een versie gebruikt, selecteert u een versie in de lijst en vervolgens **Wijzig de naam van** via de werkbalk. Voer nieuwe naam en selecteer **gedaan**. 
* Naar **exporteren** een versie gebruikt, selecteert u een versie in de lijst en vervolgens **Export app** via de werkbalk. Het bestand wordt gedownload naar uw lokale computer. 

