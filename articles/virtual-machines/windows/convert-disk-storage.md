---
title: Converter os Azure managed armazenamento de discos de Standard para Premium ou Premium para Standard | Documentos da Microsoft
description: Como converter Azure discos geridos de Standard para Premium ou Premium para Standard com o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: f97140ffeed9115a0308215ea082baee611501fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009882"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerido

Há quatro opções para o Azure discos geridos: Armazenamento de disco do Azure de Ultra, Premium SSD, Standard SSD e Standard HDD. Pode alternar entre esses tipos de armazenamento com base nas suas necessidades de desempenho pequeno período de indisponibilidade. Esta funcionalidade não é suportada para discos não geridos. Mas pode facilmente [converter um disco não gerido para um disco gerido](convert-unmanaged-to-managed-disks.md) para ser capaz de alternar entre tipos de disco.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Porque a conversão necessita de um reinício da máquina virtual (VM), deve agendar a migração do armazenamento de disco durante uma janela de manutenção pré-existente.
* Se o disco for não gerido, em primeiro lugar [convertê-lo para um disco gerido](convert-unmanaged-to-managed-disks.md) para que pode alternar entre opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Alternar todos os discos geridos de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM a partir de Standard para o armazenamento Premium ou de Premium para o armazenamento Standard. Para utilizar discos geridos Premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também muda para um tamanho que suporte o armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Mude os discos geridos individuais entre o Standard e Premium

Para sua carga de trabalho de programador/teste, pode desejar uma mistura de discos Standard e Premium para reduzir os custos. Pode optar por atualizar apenas os discos que têm um melhor desempenho. Este exemplo mostra como converter um disco VM único de Standard para o armazenamento Premium ou de Premium para o armazenamento Standard. Para utilizar discos geridos Premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que suporte o armazenamento Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Converter os discos geridos Standard para Premium no portal do Azure

Siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM a partir da lista de **máquinas virtuais** no portal.
3. Se a VM não está parada, selecione **parar** na parte superior da VM **descrição geral** painel e aguarde que a VM parar.
3. No painel da VM, selecione **discos** no menu.
4. Selecione o disco que pretende converter.
5. Selecione **configuração** no menu.
6. Alterar o **tipo de conta** partir **Standard HDD** para **Premium SSD**.
7. Clique em **guardar**e feche o painel de disco.

A conversão de tipo de disco é instantânea. Pode reiniciar a VM após a conversão.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Alternar entre o Standard HDD e SSD padrão, os discos geridos 

Este exemplo mostra como converter um disco VM único de padrão de HDD para Standard SSD ou de SSD Standard para Standard HDD:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passos Seguintes

Faça uma cópia só de leitura de uma VM com um [instantâneo](snapshot-copy-managed-disk.md).
