---
title: Converter o armazenamento do Azure Managed disks de Standard para Premium ou Premium para Standard
description: Como converter o armazenamento do Azure Managed disks de Standard para Premium ou Premium para Standard usando o CLI do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd3c7112a70850d137f77d28e83c60916a96ea3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036627"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter o armazenamento do Azure Managed disks de Standard para Premium ou Premium para Standard

Há quatro tipos de disco de discos gerenciados do Azure: ultra SSDs do Azure (visualização), SSD Premium, SSD padrão e HDD padrão. Você pode alternar entre os três tipos de disco de GA (SSD Premium, SSD padrão e HDD padrão) com base em suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um ultra SSD, você deve implantar um novo.

Não há suporte para essa funcionalidade em discos não gerenciados. Mas você pode [converter facilmente um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de disco.

Este artigo mostra como converter discos gerenciados de Standard para Premium ou Premium para Standard usando o CLI do Azure. Para instalar ou atualizar a ferramenta, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão de disco requer uma reinicialização da VM (máquina virtual), portanto, Agende a migração do armazenamento em disco durante uma janela de manutenção pré-existente.
* Para discos não gerenciados, primeiro [converta em discos gerenciados](convert-unmanaged-to-managed-disks.md) para que você possa alternar entre as opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Mudar todos os discos gerenciados de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM do armazenamento Standard para Premium ou do armazenamento Premium para Standard. Para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](sizes.md) que dá suporte ao armazenamento Premium. Este exemplo também muda para um tamanho que dá suporte ao armazenamento Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos gerenciados individuais entre Standard e Premium

Para sua carga de trabalho de desenvolvimento/teste, talvez você queira ter uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco de VM do armazenamento Standard para Premium ou do armazenamento Premium para Standard. Para usar o Premium Managed disks, sua VM deve usar um [tamanho de VM](sizes.md) que dá suporte ao armazenamento Premium. Este exemplo também muda para um tamanho que dá suporte ao armazenamento Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Mudar os discos gerenciados entre HDD Standard e SSD Standard

Este exemplo mostra como converter um único disco de VM de HDD Standard para SSD Standard ou de SSD Standard para HDD Standard.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Mudar os discos gerenciados entre Standard e Premium no portal do Azure

Siga estes passos.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de **máquinas virtuais**.
3. Se a VM não estiver parada, selecione **parar** na parte superior do painel **visão geral** da VM e aguarde a interrupção da VM.
4. No painel da VM, selecione **discos** no menu.
5. Selecione o disco que você deseja converter.
6. Selecione **configuração** no menu.
7. Altere o **tipo de conta** de **HDD Standard** para **SSD Premium** ou de **SSD Premium** para **HDD Standard**.
8. Selecione **salvar**e feche o painel de disco.

A atualização do tipo de disco é instantânea. Você pode reiniciar a VM após a conversão.

## <a name="next-steps"></a>Passos seguintes

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).
