---
title: Azure Event Grid-beveiliging en verificatie
description: Beschrijving van Azure Event Grid en de concepten ervan.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: babanisa
ms.openlocfilehash: 2fd8712cbe5d34baed158a56e6f06b6235f5d4b2
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068181"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid-beveiliging en verificatie 

Azure Event Grid heeft drie typen verificatie:

* Levering van de WebHook-gebeurtenissen
* Gebeurtenisabonnementen
* Aangepast onderwerp publiceren

## <a name="webhook-event-delivery"></a>Levering van gebeurtenissen voor WebHook

Webhooks vormen een van de vele manieren voor het ontvangen van gebeurtenissen uit Azure Event Grid. Wanneer een nieuwe gebeurtenis klaar is, berichten EventGrid service een HTTP-aanvraag naar de geconfigureerde eindpunt met de gebeurtenis in de aanvraagtekst.

Net als vele andere services die webhooks ondersteunen, moet u 'eigenaar' van de Webhook-eindpunt bewijzen voordat er begonnen wordt met het leveren van gebeurtenissen naar dit eindpunt EventGrid. Deze vereiste is om te voorkomen dat een nietsvermoedende eindpunt om het doel-eindpunt voor de bezorging van gebeurtenissen uit EventGrid. Echter, wanneer u een van de drie Azure-services die hieronder worden vermeld, de Azure-infrastructuur verwerkt automatisch deze validatie:

* Azure Logic Apps,
* Azure Automation
* Azure Functions voor EventGrid Trigger.

Als u een ander type eindpunt, zoals Azure-functie op basis van een HTTP-trigger, moet de code van uw eindpunt om deel te nemen in een validatie-handshake met EventGrid. EventGrid ondersteunt twee verschillende validatie handshake modellen:

1. **ValidationCode handshake**: op het moment van de event-abonnement maken, EventGrid plaatst een 'abonnement validatiegebeurtenis' aan uw eindpunt. Het schema van deze gebeurtenis is vergelijkbaar met een andere EventGridEvent en het gegevensgedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Wanneer uw toepassing heeft vastgesteld dat de validatieaanvraag voor de voor een verwachte gebeurtenisabonnement is, moet de toepassingscode echo weer de validatiecode voor het EventGrid reageert. Dit mechanisme handshake wordt ondersteund in alle EventGrid versies.

2. **ValidationURL handshake (handmatige handshake)**: In bepaalde gevallen hebt u geen controle over de broncode van het eindpunt te kunnen zijn voor het implementeren van de handshake ValidationCode op basis van. Bijvoorbeeld, als u een service van derden gebruiken (zoals [Zapier](https://zapier.com) of [IFTTT](https://ifttt.com/)), mogelijk niet via een programma reageert met de code voor validatie. Beginnen met 2018-05-01-preview-versie, EventGrid biedt nu ondersteuning voor een handshake handmatig worden gevalideerd. Als u een gebeurtenisabonnement met behulp van SDK/hulpprogramma's die gebruikmaken van deze nieuwe API-versie (2018-05-01-preview), EventGrid verzendt maakt een `validationUrl` eigenschap als onderdeel van de gegevens van het abonnement validatie-gebeurtenis. Voor het voltooien van de handshake alleen een GET aanvragen op die URL, via een REST-client of via uw webbrowser. De validatie van de opgegeven URL is alleen voor ongeveer 10 minuten geldig. Gedurende deze periode kan de Inrichtingsstatus van het gebeurtenisabonnement is `AwaitingManualAction`. Als u de handmatige validatie binnen 10 minuten niet voltooit, de Inrichtingsstatus is ingesteld op `Failed`. Hebt u om het gebeurtenisabonnement opnieuw voordat u de handmatige validatie probeert te maken.

Dit mechanisme van handmatige validatie is beschikbaar als preview. Als u de functie wilt gebruiken, moet u de [Event Grid-extensie](/cli/azure/azure-cli-extensions-list) voor de [Azure CLI](/cli/azure/install-azure-cli) installeren. U kunt deze installeren met `az extension add --name eventgrid`. Als u de REST-API, zorg ervoor dat u `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Validatiedetails

* Op het moment van gebeurtenis-abonnement maken/bijwerken plaatst Event Grid een gebeurtenis van de validatie-abonnement naar het doel-eindpunt. 
* De gebeurtenis bevat de waarde van een header ' aeg gebeurtenistype: SubscriptionValidation '.
* De hoofdtekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
* De eigenschap type gebeurtenis van de gebeurtenis is `Microsoft.EventGrid.SubscriptionValidationEvent`.
* De eigenschap gegevens van de gebeurtenis bevat een `validationCode` eigenschap met een willekeurige tekenreeks. Bijvoorbeeld, "validationCode: acb13... '.
* Als u API-versie 2018-05-01-preview gebruikt, bevat gegevens van de gebeurtenis ook een `validationUrl` eigenschap met een URL voor het handmatig valideren van het abonnement.
* De matrix bevat alleen de validatiegebeurtenis. Andere gebeurtenissen worden verzonden in een afzonderlijke aanvraag nadat u echo terug van de code voor validatie.
* De EventGrid DataPlane-SDK's zijn klassen die overeenkomt met de abonnement-validatie-gebeurtenisgegevens en abonnement validatie-antwoord.

Een voorbeeld SubscriptionValidationEvent wordt weergegeven in het volgende voorbeeld:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Om te bewijzen dat eindpunt eigendom, echo wordt teruggestuurd de validatiecode in de eigenschap validationResponse, zoals wordt weergegeven in het volgende voorbeeld:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Of u kunt handmatig het abonnement valideren door een GET-aanvraag verzenden naar de URL van de validatie. Het gebeurtenisabonnement blijft in behandeling totdat gevalideerd.

U vindt een C#-voorbeeld dat laat hoe u zien voor het afhandelen van de abonnement-validatie-handshake op https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs.

### <a name="checklist"></a>Controlelijst

Tijdens de event-abonnement maken, als er een foutbericht weergegeven zoals "de poging om te valideren van het opgegeven eindpunt https://your-endpoint-here is mislukt. Ga voor meer informatie naar https://aka.ms/esvalidation', betekent dit dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen, controleert u of de volgende aspecten:

* Hebt u controle over de toepassingscode in de doel-eindpunt? Bijvoorbeeld, als u een HTTP-trigger op basis van Azure-functie schrijft, hebt u toegang tot de toepassingscode wijzigingen aanbrengen?
* Hebt u toegang tot de toepassingscode, de ValidationCode op basis van handshake-mechanisme implementeren zoals wordt weergegeven in het bovenstaande voorbeeld.

* Als u geen toegang tot de toepassingscode (bijvoorbeeld als u een service van derden die webhooks ondersteunen), kunt u de handmatige handshake-mechanisme. Zorg ervoor dat u de API-versie 2018-05-01-preview of hoger (installatie Event Grid-Azure CLI-extensie) voor het ontvangen van de validationUrl in de validatiegebeurtenis. Voor het voltooien van de handshake handmatig worden gevalideerd, haal de waarde van de `validationUrl` eigenschap en gaat u naar deze URL in uw webbrowser. Als de validatie is gelukt, ziet u een bericht in uw webbrowser validatie is gelukt. U ziet dat het gebeurtenisabonnement provisioningState is 'geslaagd'. 

### <a name="event-delivery-security"></a>Gebeurtenis levering beveiliging

U kunt de webhook-eindpunt kunt beveiligen door queryparameters toevoegen aan de webhook-URL bij het maken van een gebeurtenisabonnement. Stel een van deze queryparameters moet een geheim, zoals een [toegangstoken](https://en.wikipedia.org/wiki/Access_token) die de webhook kunt gebruiken voor het herkennen van de gebeurtenis is afkomstig van Event Grid met geldige machtigingen. Event Grid bevat deze queryparameters in elke bezorging van gebeurtenissen naar de webhook.

Tijdens het bewerken van het gebeurtenisabonnement, de queryparameters worden niet weergegeven of geretourneerd, tenzij de [--opnemen-full-eindpunt-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter wordt gebruikt in Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belangrijk te weten dat Azure Event Grid biedt alleen ondersteuning voor HTTPS-webhook-eindpunten.

## <a name="event-subscription"></a>Gebeurtenisabonnement

U moet bewijzen dat u toegang tot de gebeurtenisbron en de handler hebt om u te abonneren op een gebeurtenis. Aan te tonen dat u eigenaar van een WebHook is in de voorgaande sectie behandeld. Als u een gebeurtenis-handler die niet van een WebHook (zoals een event hub of queue-opslag), moet u toegang voor schrijven naar die resource. Deze machtigingscontrole voorkomt dat een onbevoegde gebruiker verzenden van gebeurtenissen naar uw resource.

Hebt u de **Microsoft.EventGrid/EventSubscriptions/Write** machtiging voor de desbetreffende bron van de gebeurtenis. U moet deze machtiging namelijk u een nieuw abonnement in het bereik van de resource schrijft. De vereiste resource verschilt afhankelijk van of u bent u zich op een systeemonderwerp of een aangepast onderwerp abonneert. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeemonderwerpen (uitgevers voor Azure-service)

Voor onderwerpen over het systeem moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de resource publiceren van de gebeurtenis. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Bijvoorbeeld, om u te abonneren op een gebeurtenis op een storage-account met de naam **MIJNACCT**, moet u de machtiging Microsoft.EventGrid/EventSubscriptions/Write op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de event grid-onderwerp. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Bijvoorbeeld, om u te abonneren op een aangepast onderwerp met de naam **mytopic**, moet u de machtiging Microsoft.EventGrid/EventSubscriptions/Write op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepast onderwerp publiceren

Aangepaste onderwerpen Shared Access Signature (SAS) of sleutelverificatie gebruiken. We raden SAS, maar sleutelverificatie biedt eenvoudige programmeren en is compatibel met veel bestaande webhook uitgevers. 

De verificatie-waarde in de HTTP-header te nemen. Gebruik voor SAS, **aeg-sas-token** voor de headerwaarde. Gebruik voor verificatie met een sleutel, **aeg-sas-sleutel** voor de headerwaarde.

### <a name="key-authentication"></a>Verificatie met een sleutel

Verificatie met een sleutel is de eenvoudigste vorm van verificatie. Gebruik de volgende indeling: `aeg-sas-key: <your key>`

Bijvoorbeeld, doorgeven u een sleutel met:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid zijn de bron, een verlooptijd en een handtekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}`.

De resource is het pad voor de event grid-onderwerp waarmee u gebeurtenissen verzendt. Bijvoorbeeld, is een geldige resource-pad: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

U de handtekening van een sleutel genereren.

Bijvoorbeeld, een geldige **aeg-sas-token** waarde is:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Het volgende voorbeeld wordt een SAS-token voor gebruik met Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Beheer van Access Control

Azure Event Grid kunt u bepalen het niveau van toegang krijgen tot verschillende gebruikers verschillende beheerbewerkingen zoals gebeurtenisabonnementen lijst, een nieuwe maken en genereren van sleutels. Event Grid maakt gebruik van Azure rol op basis van toegang controleren (RBAC).

### <a name="operation-types"></a>Bewerkingstypen

Azure event grid ondersteunt de volgende acties:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De laatste drie bewerkingen retourneren mogelijk geheime gegevens die buiten het normale leesbewerkingen wordt gefilterd. Het raadzaam dat u toegang tot deze bewerkingen beperken. Aangepaste rollen kunnen worden gemaakt met [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [Azure-opdrachtregelinterface (CLI)](../role-based-access-control/role-assignments-cli.md), en de [REST-API](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Rol afdwingen op basis van de toegangscontrole (RBAC)

Gebruik de volgende stappen uit om af te dwingen van RBAC voor verschillende gebruikers:

#### <a name="create-a-custom-role-definition-file-json"></a>Maak een aangepaste rol definitie-bestand (.json)

Hieronder vindt u voorbeeld Event Grid roldefinities die toestaat dat gebruikers verschillende acties uitvoeren.

**EventGridReadOnlyRole.json**: alleen kunnen alleen-lezen bewerkingen.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: toestaan, maar weigeren verwijderingsacties beperkte post-acties.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: kunnen alle event grid-acties.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Maken en toewijzen van de aangepaste rol met Azure CLI

Gebruik het volgende voor het maken van een aangepaste rol:

```azurecli
az role definition create --role-definition @<file path>
```

Als u wilt de rol toewijzen aan een gebruiker, gebruikt u:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Volgende stappen

* Zie voor een inleiding tot Event Grid, [over Event Grid](overview.md)
