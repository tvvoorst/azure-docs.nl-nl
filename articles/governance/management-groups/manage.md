---
title: Wijzigen, verwijderen of uw beheergroepen in Azure beheren
description: Informatie over het onderhouden en bijwerken van uw hiërarchie beheren.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rithorn
ms.openlocfilehash: a3de0df8fde3b271b7ba9bb9aab01dbcd5c3bf08
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991211"
---
# <a name="manage-your-resources-with-management-groups"></a>Uw resources beheren met beheergroepen

Beheergroepen zijn containers waarmee u toegang, beleid en naleving beheren voor meerdere abonnementen. U kunt wijzigen, verwijderen en beheren van deze containers om hiërarchieën die kunnen worden gebruikt met [Azure Policy](../policy/overview.md) en [Azure rollen gebaseerde toegang besturingselementen (RBAC)](../../role-based-access-control/overview.md). Zie voor meer informatie over het van beheergroepen, [organiseren van uw resources met Azure-beheergroepen](overview.md).

Als u wilt wijzigingen aanbrengt aan een beheergroep, moet u een rol eigenaar of bijdrager hebben voor de beheergroep. Om te zien welke machtigingen u hebt, selecteert u de beheergroep en selecteer vervolgens **IAM**. Zie voor meer informatie over RBAC-rollen, [beheren van toegang en machtigingen met RBAC](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Wijzig de naam van een beheergroep

U kunt de naam van de beheergroep wijzigen met behulp van de portal, PowerShell of Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Wijzig de naam in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u wilt wijzigen.

1. Selecteer de **naam van groep** optie aan de bovenkant van de pagina.

   ![Naam van groep wijzigen](./media/detail_action_small.png)

1. Wanneer het menu wordt geopend, typt u de nieuwe naam die u wilt weergeven.

   ![Naam van groep wijzigen](./media/rename_context.png)

1. Selecteer **Opslaan**.

### <a name="change-the-name-in-powershell"></a>Wijzig de naam in PowerShell

Bijwerken van het gebruik van de naam weergegeven **Update AzureRmManagementGroup**. Bijvoorbeeld, als wilt wijzigen de naam van een management groepen van 'Contoso IT' naar 'Contoso groeperen', voert u de volgende opdracht uit:

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Wijzig de naam in de Azure CLI

Gebruik de opdracht update voor Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Een beheergroep verwijderen

Als u wilt verwijderen van een beheergroep, moeten de volgende vereisten worden voldaan:

1. Er zijn geen onderliggende beheergroepen of de abonnementen voor de beheergroep.

   - Zie voor het verplaatsen van een abonnement uit een beheergroep, [abonnement verplaatsen naar een andere beheergroep](#Move-subscriptions-in-the-hierarchy).

   - Zie voor het verplaatsen van een beheergroep met een andere beheergroep, [beheergroepen verplaatsen in de hiërarchie](#Move-management-groups-in-the-hierarchy).

1. U hebt schrijfmachtigingen voor de groep eigenaar of bijdrager beheerrol in de beheergroep. Om te zien welke machtigingen u hebt, selecteert u de beheergroep en selecteer vervolgens **IAM**. Zie voor meer informatie over RBAC-rollen, [beheren van toegang en machtigingen met RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>In de portal verwijderen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u wilt verwijderen.

1. Selecteer **verwijderen**

   - Als het pictogram is uitgeschakeld, ziet de muis selector muiswijzer op het pictogram u de reden.

   ![Groep verwijderen](./media/delete.png)

1. Er is een venster dat wordt geopend waaruit blijkt dat u wilt verwijderen van de beheergroep.

   ![Groep verwijderen](./media/delete_confirm.png)

1. Selecteer **Ja**.

### <a name="delete-in-powershell"></a>Verwijderen in PowerShell

Gebruik de **Remove-AzureRmManagementGroup** opdracht vanuit PowerShell beheergroepen verwijderen.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Verwijderen in de Azure CLI

Met Azure CLI, gebruikt u de opdracht az account management-groep verwijderen.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Beheergroepen weergeven

Hier vindt u een beheergroep die u hebt een directe of overgenomen RBAC-rol op.  

### <a name="view-in-the-portal"></a>Weergeven in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. De hiërarchie beheren pagina geladen waar u alle beheergroepen en abonnementen die u toegang tot hebt kunt bekijken. De groepsnaam van de selecteren gaat u naar beneden een niveau in de hiërarchie. De navigatie werkt op dezelfde manier als een bestand in bestandenverkenner.

   ![Algemeen](./media/main.png)

1. De details van de beheergroep, selecteer de **(details)** koppelen naast de titel van de beheergroep. Als deze koppeling niet beschikbaar is, hebt u machtigingen om deze beheergroep weer te geven.  

### <a name="view-in-powershell"></a>Weergeven in PowerShell

U kunt de opdracht Get-AzureRmManagementGroup gebruiken om op te halen van alle groepen.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Voor een enkele beheergroep informatie gebruikt u de parameter - groepsnaam

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="view-in-azure-cli"></a>Weergeven in de Azure CLI

U de opdracht lijst gebruiken om op te halen van alle groepen.  

```azurecli-interactive
az account management-group list
```

Voor een enkele beheergroep informatie gebruikt u de opdracht weergeven

```azurecli-interactive
az account management-group show --name 'Contoso'
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Verplaatsen van abonnementen in de hiërarchie

Eén reden om te maken van een beheergroep is abonnementen samen bundelen. Alleen de beheergroepen en abonnementen kunnen onderliggende elementen van een andere beheergroep worden gemaakt. Een abonnement dat wordt verplaatst naar een beheergroep neemt alle toegang van gebruikers en het beleid van de bovenliggende beheergroep.

Voor het verplaatsen van het abonnement, zijn er enkele machtigingen die u moet hebben:

- De rol van de 'Eigenaar' van het onderliggende-abonnement.
- 'Eigenaar' of ' ' de rol Inzender op de nieuwe bovenliggende beheergroep.
- 'Eigenaar' of ' ' de rol Inzender op de oude bovenliggende beheergroep.

Om te zien welke machtigingen u hebt, selecteert u de beheergroep en selecteer vervolgens **IAM**. Zie voor meer informatie over RBAC-rollen, [beheren van toegang en machtigingen met RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Verplaatsen van abonnementen in de portal

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Een bestaand abonnement toevoegen aan een beheergroep

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent om te worden van het bovenliggende item.

1. Aan de bovenkant van de pagina, selecteer **abonnement toevoegen**.

1. Selecteer het abonnement in de lijst met de juiste ID.

   ![Onderliggende items](./media/add_context_sub.png)

1. Selecteer 'Opslaan'.

#### <a name="remove-a-subscription-from-a-management-group"></a>Een abonnement verwijdert uit een beheergroep

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent dat wil zeggen de huidige bovenliggende.  

1. Selecteer de ellips aan het einde van de rij voor het abonnement in de lijst die u wilt verplaatsen.

   ![Verplaatsen](./media/move_small.png)

1. Selecteer **verplaatsen**.

1. Selecteer op het menu dat verschijnt, de **bovenliggende beheergroep**.

   ![Verplaatsen](./media/move_small_context.png)

1. Selecteer **Opslaan**.

### <a name="move-subscriptions-in-powershell"></a>Verplaatsen van abonnementen in PowerShell

Voor het verplaatsen van een abonnement in PowerShell, gebruikt u de opdracht Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

De koppeling tussen verwijderen en het abonnement en de beheergroep die u gebruikt de opdracht Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Verplaatsen van abonnementen in Azure CLI

Voor het verplaatsen van een abonnement in de CLI, gebruikt u de opdracht toevoegen.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Als u wilt verwijderen van het abonnement uit de beheergroep, de abonnement-remove-opdracht te gebruiken.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Beheergroepen verplaatsen in de hiërarchie  

Wanneer u een bovenliggende beheergroep, alle onderliggende resources met beheergroepen, abonnementen, resourcegroepen en resources verplaatsen met de bovenliggende verplaatsen.

### <a name="move-management-groups-in-the-portal"></a>Beheergroepen verplaatsen in de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** > **beheergroepen**.

1. Selecteer de beheergroep die u van plan bent om te worden van het bovenliggende item.

1. Aan de bovenkant van de pagina, selecteer **beheergroep toevoegen**.

1. Selecteer in het menu dat verschijnt, als u wilt dat een nieuwe of een bestaande beheergroep.

   - Nieuw selecteren, wordt een nieuwe beheergroep maken.
   - Selecteren van een bestaande, geeft u met een vervolgkeuzelijst van alle beheergroepen die u naar deze beheergroep verplaatsen kunt.  

   ![Verplaatsen](./media/add_context_MG.png)

1. Selecteer **Opslaan**.

### <a name="move-management-groups-in-powershell"></a>Verplaatsen van beheergroepen in PowerShell

Gebruik de opdracht Update AzureRmManagementGroup in PowerShell om te verplaatsen van een beheergroep onder een andere groep.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Beheergroepen verplaatsen in de Azure CLI

Gebruik de opdracht update voor het verplaatsen van een beheergroep met Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beheergroepen:

- [Resources organiseren met beheergroepen voor Azure](overview.md)
- [Beheergroepen voor het ordenen van Azure-resources maken](create.md)
- [De Azure Powershell-module installeren](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Controleer de REST API-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [De Azure CLI-extensie installeren](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)