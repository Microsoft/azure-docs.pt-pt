---
title: Converter os Azure managed armazenamento de discos de standard para premium e vice-versa | Documentos da Microsoft
description: Como converter do Azure gerido armazenamento de discos de standard para premium e vice-versa, ao utilizar a CLI do Azure.
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
ms.openlocfilehash: 6b78027191d72c10b20c9d09a92c82be4a9e3e05
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650806"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter os Azure managed armazenamento de discos de standard para premium e vice-versa

Discos geridos oferece quatro [tipo de disco](disks-types.md) opções: Unidade de ultra de estado sólido (SSD), Premium SSD, padrões SSD e unidade de disco de rígido Standard (HDD). Permite-lhe alternar facilmente entre as opções com o tempo de inatividade mínimo, com base nas suas necessidades de desempenho. Isto não é suportado para discos não geridos. Mas pode facilmente [converter para discos geridos](convert-unmanaged-to-managed-disks.md) alternar facilmente entre os tipos de disco.

Este artigo mostra-lhe como converter discos geridos de standard para premium e vice-versa, utilizando a CLI do Azure. Se precisar de instalar ou atualizá-lo, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar

* A conversão necessita de um reinício da VM, por isso, agende a migração do armazenamento de discos durante uma janela de manutenção pré-existente. 
* Se estiver a utilizar discos não geridos, primeiro [converter para discos geridos](convert-unmanaged-to-managed-disks.md) para utilizar este artigo para alternar entre as opções de armazenamento.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter todos os discos geridos de uma VM de standard para premium e vice-versa

O exemplo seguinte mostra como mudar todos os discos de uma VM de standard para o armazenamento premium. Para utilizar discos geridos premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento premium. Este exemplo também muda para um tamanho que suporte o armazenamento premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um disco gerido de standard para premium e vice-versa

Para sua carga de trabalho de programador/teste, pode querer ter a mistura de discos standard e premium para reduzir o custo. Poderá fazê-lo com a atualização para o armazenamento premium, apenas os discos que exigem um melhor desempenho. O exemplo seguinte mostra como alternar de um único disco de uma VM do plano padrão para o armazenamento premium e vice-versa. Para utilizar discos geridos premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento premium. Este exemplo também muda para um tamanho que suporte o armazenamento premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Converter um disco gerido standard HDD para SSD standard e vice-versa

O exemplo seguinte mostra como alternar de um único disco de uma VM do padrão HDD para standard SSD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-managed-disks-between-standard-and-premium-in-azure-portal"></a>Converter os discos geridos entre o standard e premium no portal do Azure

Pode converter os discos geridos entre o standard e premium no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM a partir da lista de **máquinas virtuais** no portal.
3. Se a VM não estiver parada, clique em **parar** na parte superior do painel de descrição geral da VM e aguarde que a VM parar.
3. No painel da VM, selecione **discos** no menu.
4. Selecione o disco que pretende converter.
5. Selecione **configuração** no menu.
6. Alterações a **tipo de conta** partir **Standard HDD** para **Premium SSD**e vice-versa.
7. Clique em **guardar** e feche o painel de disco.

A atualização do tipo de disco está em vigor instantânea. Pode reiniciar a VM após a conversão.

## <a name="next-steps"></a>Passos Seguintes

Ter uma cópia só de leitura de uma VM ao utilizar [instantâneos](snapshot-copy-managed-disk.md).

