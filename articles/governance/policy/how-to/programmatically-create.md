---
title: Programmatisch beleid maken en Nalevingsgegevens met Azure Policy weergeven
description: Dit artikel helpt u bij het programmatisch beleid maken en beheren voor Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dd7ec4f1d0c018a3c7eed19bea523f7c09bfea3e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985313"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programmatisch beleid maken en compatibiliteitsgegevens weergeven

Dit artikel helpt u bij het programmatisch beleid maken en beheren. Beleidsdefinities worden verschillende regels en effecten voor uw resources afdwingen. Afdwingen zorgt ervoor dat resources voldoen aan uw bedrijfsnormen en serviceovereenkomsten blijven.

Zie voor meer informatie over de naleving van [ophalen van Nalevingsgegevens](getting-compliance-data.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorg ervoor dat de volgende vereisten wordt voldaan:

1. Installeer de [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-API’s.

1. Werk uw PowerShell-module van Azure RM bij naar de nieuwste versie. Zie voor meer informatie over de nieuwste versie, [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registreer de resourceprovider Policy Insights met behulp van Azure PowerShell om ervoor te zorgen dat uw abonnement hiervoor met de resourceprovider geschikt. Als u een resourceprovider wilt registreren, moet u toestemming hebben om de registreeractie voor de resourceprovider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

   ```azurepowershell-interactive
   Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Zie voor meer informatie over het registreren en weergeven van resourceproviders [Resourceproviders en typen](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Als u niet hebt gedaan, installeert u Azure CLI. Krijgt u de nieuwste versie [Azure CLI installeren op Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Maken en een beleidsdefinitie toewijzen

De eerste stap voor beter inzicht in uw resources is het maken en toewijzen van beleid voor uw resources. De volgende stap is om te leren hoe u programmatisch maken en toewijzen van een beleid. De voorbeeldbeleid controleert storage-accounts die zijn geopend met alle openbare netwerken met behulp van PowerShell, Azure CLI en HTTP-aanvragen.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Maken en toewijzen van een beleidsdefinitie met PowerShell

1. Gebruik de volgende JSON-codefragment om te maken van een JSON-bestand met de naam AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Zie voor meer informatie over het ontwerpen van een beleidsdefinitie [structuur van beleidsdefinities Azure](../concepts/definition-structure.md).

1. Voer de volgende opdracht om te maken van een beleidsdefinitie met behulp van het bestand AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   De opdracht maakt u een beleidsdefinitie met de naam _Audit Storage Accounts openen met een openbaar netwerk_. Zie voor meer informatie over andere parameters die u kunt gebruiken, [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

1. Nadat u de beleidsdefinitie van uw maakt, kunt u een beleidstoewijzing maken door het uitvoeren van de volgende opdrachten:

   ```azurepowershell-interactive
   $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Vervang _ContosoRG_ met de naam van de beoogde resourcegroep.

Zie voor meer informatie over het beheren van de resource-beleidsregels met behulp van de Azure Resource Manager PowerShell-module [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Maken en toewijzen van een beleidsdefinitie ARMClient

Gebruik de volgende procedure om de beleidsdefinitie van een te maken.

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand. Noem het bestand in de volgende stap.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Maak de beleidsdefinitie met een van de volgende aanroepen:

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   De voorgaande {subscriptionId} vervangen door de ID van uw abonnement of {managementGroupId} met de ID van uw [beheergroep](../../management-groups/overview.md).

   Zie voor meer informatie over de structuur van de query, [beleidsdefinities – maken of bijwerken](/rest/api/resources/policydefinitions/createorupdate) en [beleidsdefinities – maken of bijwerken op beheergroep](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Gebruik de volgende procedure een beleidstoewijzing maken en toewijzen van de beleidsdefinitie op het niveau van de resource.

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand voor het toewijzen van beleid. Vervang voorbeeldinformatie in &lt; &gt; symbolen door uw eigen waarden.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Maak de toewijzing van beleid met behulp van de volgende oproep verzenden:

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Vervang voorbeeldinformatie in &lt; &gt; symbolen door uw eigen waarden.

   Zie voor meer informatie over het maken van HTTP-aanroepen naar de REST-API, [Azure REST API-Resources](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Maken en toewijzen van een beleidsdefinitie met Azure CLI

Gebruik de volgende procedure voor het maken van een beleidsdefinitie:

1. Kopieer de volgende JSON-fragment voor het maken van een JSON-bestand voor het toewijzen van beleid.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

1. Voer de volgende opdracht om een beleidsdefinitie maken:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. Gebruik de volgende opdracht om een beleidstoewijzing te maken. Vervang voorbeeldinformatie in &lt; &gt; symbolen door uw eigen waarden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

U kunt de Beleidstoewijzings-ID opvragen met behulp van PowerShell met de volgende opdracht:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

De beleidstoewijzings-ID voor de beleidsdefinitie die u hebt gemaakt, moet lijken op het volgende voorbeeld:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Zie voor meer informatie over hoe u resourcebeleid met Azure CLI kunt beheren, [Resourcebeleid voor Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de opdrachten en query's in dit artikel.

- [Azure REST API-Resources](/rest/api/resources/)
- [PowerShell voor Azure RM-Modules](/powershell/module/azurerm.resources/#policies)
- [Azure CLI-opdrachten voor beleid](/cli/azure/policy?view=azure-cli-latest)
- [Resourceprovider Policy Insights REST API-naslaginformatie](/rest/api/policy-insights)
- [Resources organiseren met beheergroepen voor Azure](../../management-groups/overview.md)