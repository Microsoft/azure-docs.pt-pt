---
title: Carregar um VHD generalizado no Exemplo de Script do PowerShell do Azure | Microsoft Docs
description: Script de exemplo do PowerShell para carregar um VHD generalizado para o Azure e criar uma nova VM com o modelo de implementação do gestor de recursos e o Managed Disks.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bd1be056576275194a013671cf0aae1f7855df69
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749696"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>Script de exemplo para carregar um VHD para o Azure e criar uma nova VM

Este script utiliza um ficheiro. vhd local de uma VM generalizada e carrega-o para o Azure, cria uma imagem do Disco Gerido e utiliza-a para criar uma nova VM.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando                                                                                                             | Notas                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)                           | Cria um grupo de recursos no qual todos os recursos são armazenados.                                                                                                                          |
| [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)                         | Cria uma conta de armazenamento.                                                                                                                                                           |
| [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)                                               | Carrega um disco rígido virtual a partir de uma máquina virtual no local para um blob numa conta de armazenamento na nuvem no Azure.                                                                       |
| [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig)                               | Cria um objeto de imagem configurável.                                                                                                                                                 |
| [Set-AzImageOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azimageosdisk)                               | Define as propriedades de disco do sistema operativo para um objeto de imagem.                                                                                                                        |
| [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)                                           | Cria uma nova imagem.                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual.                                                                                |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)                         | Cria uma rede virtual.                                                                                                                                                           |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress)                       | Cria um endereço IP público.                                                                                                                                                         |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)                     | Cria uma interface de rede.                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)   | Cria uma configuração de regra do grupo de segurança de rede. Esta configuração serve para criar uma regra de NSG quando o NSG é criado.                                                       |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup)             | Cria um grupo de segurança de rede.                                                                                                                                                    |
| [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)                         | Obtém uma rede virtual num grupo de recursos.                                                                                                                                          |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig)                                     | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage)                           | Especifica uma imagem para uma máquina virtual.                                                                                                                                            |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)                                     | Define as propriedades de disco do sistema operativo para uma máquina virtual.                                                                                                                      |
| [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)                   | Define as propriedades de disco do sistema operativo para uma máquina virtual.                                                                                                                      |
| [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)                 | Adiciona uma interface de rede a uma máquina virtual.                                                                                                                                       |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)                                                 | Cria uma máquina virtual.                                                                                                                                                            |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup)                     | Remove um grupo de recursos e todos os recursos contidos no grupo.                                                                                                                         |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
