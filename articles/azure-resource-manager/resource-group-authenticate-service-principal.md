---
title: Identiteit voor Azure-app maken met PowerShell | Microsoft Docs
description: Hierin wordt beschreven hoe u met Azure PowerShell een Azure Active Directory-toepassing en service-principal maakt en toegang tot resources verleent via toegangsbeheer op basis van rollen. Het laat zien hoe u de toepassing verifieert met een certificaat.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: 84b32cadbd7d574e01053b61ace1203d495983b4
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498603"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Azure PowerShell gebruiken om een service-principal met een certificaat te maken

Wanneer u een app of een script hebt waarvoor toegang tot resources vereist is, kunt u een identiteit voor de app instellen en de app met eigen referenties verifiëren. Deze identiteit staat bekend als een service-principal. Hiermee kunt u het volgende doen:

* Andere machtigingen aan de identiteit van de app toekennen dan uw eigen machtigingen. Deze machtigingen worden meestal beperkt tot de functies die met de app moeten kunnen worden uitgevoerd.
* Een certificaat voor verificatie gebruiken bij het uitvoeren van een onbewaakt script.

> [!IMPORTANT]
> In plaats van het maken van een service-principal, kunt u overwegen beheerde identiteiten voor Azure-resources voor de identiteit van uw toepassingen. Als uw code wordt uitgevoerd op een service die ondersteuning biedt voor beheerde identiteiten en toegang tot bronnen die ondersteuning bieden voor Azure Active Directory-verificatie, zijn beheerde identiteiten een betere optie voor u. Zie voor meer informatie over beheerde identiteiten voor een Azure-resources, met inbegrip van welke services momenteel, [wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md).

In dit artikel leest u hoe een service-principal maakt die zichzelf verifieert met een certificaat. Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.

Voor dit artikel hebt u de [meest recente versie](/powershell/azure/get-started-azureps) van PowerShell nodig.

## <a name="required-permissions"></a>Vereiste machtigingen

Om dit artikel te voltooien, moet u over voldoende machtigingen beschikken in uw Azure Active Directory en uw Azure-abonnement. Meer specifiek moet u een app in Azure Active Directory kunnen maken en de service-principal kunnen toewijzen aan een rol.

De eenvoudigste manier om te controleren of uw account over de juiste machtigingen beschikt, verloopt via de portal. Zie [Check required permission](resource-group-create-service-principal-portal.md#required-permissions) (Vereiste machtiging controleren).

## <a name="create-service-principal-with-self-signed-certificate"></a>Service-principal met een zelfondertekend certificaat maken

In het volgende voorbeeld wordt een eenvoudig scenario behandeld. Hierbij wordt [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) gebruikt om een service-principal met een zelfondertekend certificaat te maken, en [New AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) om de rol [Inzender](../role-based-access-control/built-in-roles.md#contributor) aan de service-principal toe te wijzen. De roltoewijzing is afgestemd op uw huidige, geselecteerde Azure-abonnement. Gebruik [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) als u een ander abonnement wilt selecteren.

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

In het voorbeeld wordt 20 seconden gewacht zodat de nieuwe service-principal kan worden doorgegeven in Azure Active Directory. Als de wachttijd in het script te kort is, wordt de foutmelding "Principal {ID} bestaat niet in de map {DIR-ID}." weergegeven. U kunt deze fout oplossen door even te wachten voordat u de opdracht **New-AzureRmRoleAssignment** opnieuw uitvoert.

U kunt de roltoewijzing beperken tot een specifieke resourcegroep met behulp van de parameter **ResourceGroupName**. Of tot een specifieke resource met behulp van de parameters **ResourceType** en **ResourceName**. 

Als u **niet beschikt over Windows 10 of Windows Server 2016**, moet u het [programma voor het genereren van zelfondertekende certificaten](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) downloaden van Microsoft Script Center. Pak de inhoud uit en importeer de cmdlet die u nodig hebt.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Vervang de volgende twee regels in het script om het certificaat te genereren.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script

Wanneer u zich aanmeldt als een service-principal, moet u de tenant-ID van de map voor uw AD-app opgeven. Een tenant is een exemplaar van Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Service-principal met certificaat van certificeringsinstantie maken

In het volgende voorbeeld wordt een service-principal gemaakt met behulp van een certificaat dat is uitgegeven door een certificeringsinstantie. De toewijzing is afgestemd op het opgegeven Azure-abonnement. De service-principal wordt toegevoegd aan de rol [Inzender](../role-based-access-control/built-in-roles.md#contributor). Als er een fout optreedt bij het toewijzen van de rol, wordt het opnieuw geprobeerd.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script
Wanneer u zich aanmeldt als een service-principal, moet u de tenant-ID van de map voor uw AD-app opgeven. Een tenant is een exemplaar van Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

De toepassings-id en tenant-id zijn geen gevoelige gegevens, dus u kunt deze rechtstreeks in uw script insluiten. Als u de tenant-id wilt ophalen, gebruikt u:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Als u de toepassings-id wilt ophalen, gebruikt u:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Referenties wijzigen

Als u de referenties voor een AD-app wilt wijzigen, bijvoorbeeld vanwege een inbreuk op de beveiliging of omdat de referenties zijn verlopen, gebruikt u de cmdlets [Remove-AzureRmADAppCredential](/powershell/module/azurerm.resources/remove-azurermadappcredential) en [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Als u alle referenties voor een toepassing wilt verwijderen, gebruikt u:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Als u een waarde voor een certificaat wilt toevoegen, maakt u een zelfondertekend certificaat zoals aangegeven in dit artikel. Vervolgens gebruikt u:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Fouten opsporen

Bij het maken van een service-principal kunnen de volgende foutmeldingen worden weergegeven:

* **Authentication_Unauthorized** (Verificatie geweigerd) of **No subscription found in the context.** (Geen abonnement gevonden in de context.) -U deze fout wordt weergegeven wanneer u uw account beschikt niet over de [vereiste machtigingen](#required-permissions) in de Azure Active Directory om een app te registreren. Normaal gesproken ziet u deze fout wanneer alleen beheerder gebruikers in uw Azure Active Directory-apps kunnen registreren, en uw account niet een beheerder. Vraag uw beheerder om u de rol van beheerder toe te wijzen, of in te stellen dat gebruikers apps mogen registreren.

* Uw account **'heeft geen autorisatie om uit te voeren actie 'Microsoft.Authorization/roleAssignments/write' over scope '/ subscriptions / {guid}'.'**  -U deze fout wordt weergegeven wanneer u uw account beschikt niet over voldoende machtigingen voor een rol toewijzen aan een identiteit. Vraag de abonnementsbeheerder om u toe te voegen aan de rol Administrator voor gebruikerstoegang.

## <a name="next-steps"></a>Volgende stappen
* Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.
* Zie de [ontwikkelaarshandleiding voor autorisatie met de Azure Resource Manager-API](resource-manager-api-authentication.md) voor gedetailleerde stappen voor het integreren van een toepassing in Azure voor het beheren van resources.
* Zie [Toepassingsobjecten en service-principalobjecten](../active-directory/develop/app-objects-and-service-principals.md) voor meer informatie over toepassingen en service-principals. 
* Zie [Verificatiescenario's voor Azure AD](../active-directory/develop/authentication-scenarios.md) voor meer informatie over Azure Active Directory-verificatie.
