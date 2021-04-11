---
title: Converter armazenamento de discos geridos entre diferentes tipos de discos usando Azure CLI
description: Como converter discos geridos pelo Azure entre os diferentes tipos de discos utilizando o Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558462"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Converter o armazenamento de discos geridos da Azure de Standard a Premium ou Premium para Standard

Existem quatro tipos de discos geridos azure: discos ultra Azure, SSD premium, SSD padrão e HDD padrão. Pode alternar entre SSD premium, SSD padrão e HDD padrão com base nas suas necessidades de desempenho. Ainda não é capaz de mudar de ou para um disco ultra, tem de implementar um novo.

Esta funcionalidade não é suportada por discos não geridos. Mas pode [facilmente converter um disco não gerido para um disco gerido](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de discos.

Este artigo mostra como converter discos geridos de um tipo de disco para outro utilizando o Azure CLI. Para instalar ou atualizar a ferramenta, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

* A conversão do disco requer um reinício da máquina virtual (VM), por isso, agende a migração do seu armazenamento em disco durante uma janela de manutenção pré-existente.
* Para discos não geridos, [converta-se](convert-unmanaged-to-managed-disks.md) primeiro em discos geridos para que possa alternar entre opções de armazenamento.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Altere todos os discos geridos de um VM entre uma conta para outra

Este exemplo mostra como converter todos os discos de um VM para armazenamento premium. No entanto, alterando a variável sku neste exemplo, pode converter o tipo de discos VM para SSD padrão ou HDD padrão. Por favor, note que Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também muda para um tamanho que suporta o armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Comutação de discos geridos individuais de um tipo de disco para outro

Para a sua carga de trabalho de dev/teste, talvez queira ter uma mistura de discos Standard e Premium para reduzir os seus custos. Pode optar por atualizar apenas os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM do armazenamento Standard para Premium. No entanto, alterando a variável sku neste exemplo, pode converter o tipo de discos VM para SSD padrão ou HDD padrão. Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também muda para um tamanho que suporta o armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Switch geriu discos de um tipo de disco para outro

Siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de **máquinas Virtuais.**
3. Se o VM não for interrompido, **selecione Pare** no topo do painel de **visão geral** do VM e aguarde que o VM pare.
4. No painel para o VM, selecione **Discos** do menu.
5. Selecione o disco que pretende converter.
6. Selecione **Configuração** do menu.
7. Altere o tipo de **Conta** do tipo de disco original para o tipo de disco pretendido.
8. **Selecione Guardar** e feche o painel de discos.

A atualização do tipo de disco é instantânea. Pode reiniciar o seu VM após a conversão.

## <a name="next-steps"></a>Passos seguintes

Faça uma cópia apenas de leitura de um VM utilizando [instantâneos](snapshot-copy-managed-disk.md).