---
title: Converter armazenamento de discos geridos entre SSD standard e premium
description: Como converter o armazenamento de discos geridos Azure de padrão a premium ou premium para padrão usando o Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 10674d15a7515b01b0df6cf37bce89f153cb9b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870690"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter o armazenamento de discos geridos da Azure de Standard a Premium ou Premium para Standard

Existem quatro tipos de discos geridos azure: discos ultra Azure, SSD premium, SSD padrão e HDD padrão. Pode alternar entre os três tipos de discos GA (SSD premium, SSD standard e HDD padrão) com base nas suas necessidades de desempenho. Ainda não é capaz de mudar de ou para um disco ultra, tem de implementar um novo.

Esta funcionalidade não é suportada por discos não geridos. Mas pode [facilmente converter um disco não gerido para um disco gerido](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de discos.

Este artigo mostra como converter discos geridos de Standard a Premium ou Premium para Standard utilizando o Azure CLI. Para instalar ou atualizar a ferramenta, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão do disco requer um reinício da máquina virtual (VM), por isso, agende a migração do seu armazenamento em disco durante uma janela de manutenção pré-existente.
* Para discos não geridos, [converta-se](convert-unmanaged-to-managed-disks.md) primeiro em discos geridos para que possa alternar entre opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Altere todos os discos geridos de um VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de um VM do armazenamento Standard para Premium ou do armazenamento Premium para Standard. Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também muda para um tamanho que suporta o armazenamento Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Altere discos geridos individuais entre Standard e Premium

Para a sua carga de trabalho de dev/teste, talvez queira ter uma mistura de discos Standard e Premium para reduzir os seus custos. Pode optar por atualizar apenas os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM de standard para armazenamento Premium ou de armazenamento Premium para Standard. Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também muda para um tamanho que suporta o armazenamento Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Discos geridos pela Switch entre o Standard HDD e o Standard SSD

Este exemplo mostra como converter um único disco VM de Standard HDD para Standard SSD ou de Standard SSD para Standard HDD.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Switch geriu discos entre Standard e Premium no portal Azure

Siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de **máquinas Virtuais.**
3. Se o VM não for interrompido, **selecione Pare** no topo do painel de **visão geral** do VM e aguarde que o VM pare.
4. No painel para o VM, selecione **Discos** do menu.
5. Selecione o disco que pretende converter.
6. Selecione **Configuração** do menu.
7. Altere o tipo de **Conta** de **Standard HDD** para **Premium SSD** ou de **Premium SSD** para Standard **HDD**.
8. **Selecione Guardar**e feche o painel de discos.

A atualização do tipo de disco é instantânea. Pode reiniciar o seu VM após a conversão.

## <a name="next-steps"></a>Passos seguintes

Faça uma cópia apenas de leitura de um VM utilizando [instantâneos](snapshot-copy-managed-disk.md).