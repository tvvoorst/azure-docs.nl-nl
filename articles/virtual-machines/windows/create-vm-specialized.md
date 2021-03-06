---
title: Een virtuele Windows-machine maken vanaf een gespecialiseerde VHD in Azure | Microsoft Docs
description: Maak een nieuwe Windows-VM door het koppelen van een gespecialiseerde beheerde schijf als de besturingssysteemschijf met in de Resource Manager-implementatiemodel.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 34bfe7733c60337d6ab7d81c498d2fb0fd15e1fd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338483"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Een Windows-VM maken vanaf een gespecialiseerde schijf met behulp van PowerShell

Maak een nieuwe virtuele machine door een gespecialiseerde beheerde schijf als de besturingssysteemschijf te koppelen. Een gespecialiseerde schijf is een kopie van de virtuele harde schijf (VHD) van een bestaande virtuele machine die wordt onderhouden door de gebruikersaccounts, toepassingen en andere statusgegevens van de oorspronkelijke virtuele machine. 

Wanneer u een nieuwe virtuele machine maken met een gespecialiseerde VHD, behoudt de nieuwe virtuele machine de naam van de computer van de oorspronkelijke virtuele machine. Andere computer-specifieke informatie is ook worden bewaard en, in sommige gevallen kan deze dubbele gegevens privacyproblemen kan veroorzaken. Houd rekening met welke typen computer-specifieke informatie voor uw toepassingen zijn afhankelijk van bij het kopiëren van een virtuele machine.

U hebt verschillende mogelijkheden:
* [Gebruik een bestaande beheerde schijf](#option-1-use-an-existing-disk). Dit is handig als u een virtuele machine die niet goed werkt. U kunt de virtuele machine opnieuw gebruiken de beheerde schijf te maken van een nieuwe virtuele machine verwijderen. 
* [Een VHD uploaden](#option-2-upload-a-specialized-vhd) 
* [Kopiëren van een bestaande VM in Azure met behulp van momentopnamen](#option-3-copy-an-existing-azure-vm)

U kunt ook de Azure portal om te gebruiken [een nieuwe virtuele machine maken vanaf een gespecialiseerde VHD](create-vm-specialized-portal.md).

Dit onderwerp ziet u hoe u beheerde schijven gebruiken. Als u een oude implementatie hebt die moet worden met behulp van een storage-account, Zie [een virtuele machine maken vanaf een gespecialiseerde VHD in een storage-account](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Voordat u begint
Als u PowerShell gebruikt, zorg ervoor dat u de nieuwste versie van de AzureRM.Compute PowerShell-module hebt. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Zie voor meer informatie, [versiebeheer in Azure PowerShell](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Optie 1: Gebruik een bestaande schijf

Als u had een virtuele machine die u hebt verwijderd en u gebruiken van de besturingssysteemschijf wilt te maken van een nieuwe virtuele machine, gebruikt u [Get-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nu kunt u deze schijf koppelen als de schijf met het besturingssysteem naar een [nieuwe virtuele machine](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Optie 2: Een gespecialiseerde VHD uploaden

U kunt de VHD van een gespecialiseerde virtuele machine gemaakt met een on-premises virtualisatie hulpprogramma, zoals Hyper-V, of een virtuele machine die zijn geëxporteerd uit een andere cloud uploaden.

### <a name="prepare-the-vm"></a>De virtuele machine voorbereiden
Als u van plan bent de VHD als gebruiken-is een nieuwe virtuele machine maakt, zorg ervoor dat de volgende stappen zijn voltooid. 
  
  * [Voorbereiden van een Windows-VHD te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Geen** generaliseren van de virtuele machine met behulp van Sysprep.
  * Verwijder alle Gast virtualisatie-hulpprogramma's en de agents die zijn geïnstalleerd op de virtuele machine (zoals VMware-hulpprogramma's).
  * Zorg ervoor dat de virtuele machine is geconfigureerd voor het ophalen van de IP-adres en DNS-instellingen via DHCP. Dit zorgt ervoor dat de server een IP-adres binnen het VNet wordt wanneer deze opgestart wordt. 


### <a name="get-the-storage-account"></a>Het opslagaccount ophalen
U moet een opslagaccount in Azure voor het opslaan van de geüploade VHD. U kunt een bestaand opslagaccount gebruiken of een nieuwe maken. 

Als u wilt de beschikbare opslag-accounts weergeven, typt u:

```powershell
Get-AzureRmStorageAccount
```

Als u een bestaand opslagaccount gebruiken wilt, gaat u verder met de [uploaden van de VHD](#upload-the-vhd-to-your-storage-account) sectie.

Als u nodig hebt voor het maken van een storage-account, als volgt te werk:

1. U moet de naam van de resourcegroep waar het opslagaccount dat moet worden gemaakt. Als u wilt weten van alle resourcegroepen in uw abonnement, typt u:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Het maken van een resourcegroep met de naam *myResourceGroup* in de *VS-West* regio, type:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Maak een opslagaccount met de naam *mystorageaccount* in deze resourcegroep met behulp van de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>De VHD uploaden naar uw storage-account 
Gebruik de [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet voor het uploaden van de VHD naar een container in uw storage-account. In dit voorbeeld wordt het bestand geüpload *myVHD.vhd* van `"C:\Users\Public\Documents\Virtual hard disks\"` naar een opslagaccount met de naam *mystorageaccount* in de *myResourceGroup* resourcegroep. Het bestand is opgeslagen in de container met de naam *mycontainer* en de nieuwe bestandsnaam worden *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Als dit lukt, krijgt u een reactie die er ongeveer als volgt uitziet:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Afhankelijk van uw netwerkverbinding en de grootte van uw VHD-bestand kan met deze opdracht even duren om uit te voeren

### <a name="create-a-managed-disk-from-the-vhd"></a>Een beheerde schijf maken op basis van de VHD

Een beheerde schijf maken op basis van de gespecialiseerde VHD in uw storage-account met [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). In dit voorbeeld wordt **myOSDisk1** voor de naam van de schijf, plaatst u de schijf in *Standard_LRS* opslag- en maakt gebruik van *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* als de URI voor de bron-VHD.

Maak een nieuwe resourcegroep voor de nieuwe virtuele machine.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nieuwe schijf met het besturingssysteem van de geüploade VHD maken. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Optie 3: Een bestaande Azure-VM kopiëren

U kunt een kopie van een virtuele machine die maakt gebruik van beheerde schijven gebruikt door het maken van een momentopname van de virtuele machine en vervolgens met behulp van deze momentopname te maken van een nieuwe schijf en een nieuwe virtuele machine beheerde maken.


### <a name="take-a-snapshot-of-the-os-disk"></a>Een momentopname van de besturingssysteemschijf

U kunt een momentopname van en volledige virtuele machine (met inbegrip van alle schijven) uitvoeren of slechts één schijf. De volgende stappen ziet u hoe u een momentopname van de besturingssysteemschijf van de virtuele machine met behulp van de [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Sommige parameters instellen. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Haal het VM-object.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
De naam van de OS-schijf ophalen

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Maak de momentopname-configuratie. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

De momentopname.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Als u van plan bent de momentopname gebruiken om te maken van een virtuele machine die moet worden hoge prestaties, gebruikt u de parameter `-AccountType Premium_LRS` met de opdracht New-AzureRmSnapshot. De parameter wordt de momentopname gemaakt, zodat deze wordt opgeslagen als een Premium Managed Disk. Premium Managed Disks is duurder dan bij Standard. Zorg dus hoeft u precies Premium voordat u de parameter.

### <a name="create-a-new-disk-from-the-snapshot"></a>Maak een nieuwe schijf van de momentopname

Een beheerde schijf maken vanuit de momentopname met [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). In dit voorbeeld wordt *myOSDisk* voor naam van de schijf.

Maak een nieuwe resourcegroep voor de nieuwe virtuele machine.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Stel de naam van de OS-schijf. 

```powershell
$osDiskName = 'myOsDisk'
```

De beheerde schijf maken.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>De nieuwe virtuele machine maken 

Resources voor netwerken en andere virtuele machine moet worden gebruikt door de nieuwe virtuele machine maken.

### <a name="create-the-subnet-and-vnet"></a>Het subNet en een vNet maken

Maak de vNet en subNet van de [virtueel netwerk](../../virtual-network/virtual-networks-overview.md).

Maak het subNet. Dit voorbeeld maakt u een subnet met de naam **mySubNet**, in de resourcegroep **myDestinationResourceGroup**, en stelt u het adres subnetvoorvoegsel op **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

Maak het vNet. In dit voorbeeld wordt de naam van het virtuele netwerk moet **myVnetName**, de locatie voor het **VS-West**, en het adresvoorvoegsel voor het virtuele netwerk naar **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerkbeveiligingsgroep en een RDP-regel maken
Als u zich aanmelden bij uw virtuele machine via RDP, moet u hebt een beveiligingsregel om RDP-toegang op poort 3389. Omdat de VHD voor de nieuwe virtuele machine is gemaakt vanuit een bestaande gespecialiseerde VM, kunt u een account van de virtuele bronmachine voor RDP.

In het volgende voorbeeld wordt de naam van de NSG op **myNsg** en de naam van de RDP-regel op **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Zie voor meer informatie over eindpunten en NSG-regels, [poorten openen voor een virtuele machine in Azure met behulp van PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Maak een openbaar IP-adres en de NIC
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

Het openbare IP-adres maken. In dit voorbeeld wordt de naam van het openbare IP-adres is ingesteld op **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Maken van de NIC. In dit voorbeeld wordt de naam van de NIC is ingesteld op **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>De VM-naam en grootte instellen

In dit voorbeeld wordt de naam van de virtuele machine ingesteld op *myVM* en de grootte van de virtuele machine op *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>De NIC toevoegen
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>De OS-schijf toevoegen 

De OS-schijf toevoegen aan de configuratie met behulp [Set-azurermvmosdisk,](/powershell/module/azurerm.compute/set-azurermvmosdisk). In dit voorbeeld wordt de grootte van de schijf moet worden ingesteld *128 GB* en koppelt u de beheerde schijf als een *Windows* besturingssysteemschijf.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>De virtuele machine voltooien 

Maakt de virtuele machine via [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)de configuraties die we zojuist hebben gemaakt.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Als deze opdracht voltooid is, ziet u uitvoer zoals de volgende:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Controleren of de virtuele machine is gemaakt
U ziet de zojuist gemaakte virtuele machine ofwel in het [Azure-portal](https://portal.azure.com)onder **Bladeren** > **virtuele machines**, of met behulp van de volgende PowerShell-opdrachten:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Aanmelden bij uw nieuwe virtuele machine. Zie voor meer informatie, [hoe u verbinding maken met en meld u aan een virtuele Azure-machine waarop Windows wordt uitgevoerd bij](connect-logon.md).

