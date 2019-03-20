---
title: Converter os Azure managed armazenamento de discos de Standard para Premium ou Premium para Standard | Documentos da Microsoft
description: Como converter do Azure geridos armazenamento de discos de Standard para Premium ou Premium para Standard com a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013625"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter os Azure managed armazenamento de discos de Standard para Premium ou Premium para Standard

Há quatro [tipos de disco](disks-types.md) para o Azure discos geridos: Armazenamento de disco do Azure de Ultra, Premium SSD, Standard SSD e Standard HDD. Pode alternar facilmente entre Premium SSD, padrão de SSD e HDD padrão com base nas suas necessidades de desempenho pequeno período de indisponibilidade. Esta funcionalidade não é suportada para discos não geridos ou Ultra de disco de armazenamento. Mas pode facilmente [convert não gerido para os managed disks](convert-unmanaged-to-managed-disks.md) para ser capaz de alternar entre tipos de disco.

Este artigo mostra como converter discos geridos de Standard para Premium ou Premium para Standard com a CLI do Azure. Para instalar ou atualizar a ferramenta, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão de discos requer um reinício da máquina virtual (VM), por isso, agende a migração do armazenamento de disco durante uma janela de manutenção pré-existente.
* Para discos não geridos, primeira [converter para discos geridos](convert-unmanaged-to-managed-disks.md) para que pode alternar entre opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alternar todos os discos geridos de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM a partir de Standard para o armazenamento Premium ou de Premium para o armazenamento Standard. Para utilizar discos geridos Premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também muda para um tamanho que suporte o armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Mude os discos geridos individuais entre o Standard e Premium

Para sua carga de trabalho de programador/teste, pode querer ter uma mistura de discos Standard e Premium para reduzir os custos. Pode optar por atualizar apenas os discos que têm um melhor desempenho. Este exemplo mostra como converter um disco VM único de Standard para o armazenamento Premium ou de Premium para o armazenamento Standard. Para utilizar discos geridos Premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também muda para um tamanho que suporte o armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar entre o Standard HDD e SSD padrão, os discos geridos

Este exemplo mostra como converter um disco VM único de padrão de HDD para Standard SSD ou de SSD Standard para HDD padrão.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Mude os discos geridos entre o Standard e Premium no portal do Azure

Siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM a partir da lista de **máquinas virtuais**.
3. Se a VM não está parada, selecione **parar** na parte superior da VM **descrição geral** painel e aguarde que a VM parar.
4. No painel da VM, selecione **discos** no menu.
5. Selecione o disco que pretende converter.
6. Selecione **configuração** no menu.
7. Alteração a **tipo de conta** partir **Standard HDD** para **Premium SSD** ou a partir de **Premium SSD** para **Standard HDD**.
8. Selecione **guardar**e feche o painel de disco.

A atualização do tipo de disco é instantânea. Pode reiniciar a VM após a conversão.

## <a name="next-steps"></a>Passos Seguintes

Faça uma cópia só de leitura de uma VM com [instantâneos](snapshot-copy-managed-disk.md).
