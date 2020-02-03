---
title: Converter o armazenamento de discos gerenciados entre SSD Standard e Premium
description: Como converter o Azure Managed disks de Standard para Premium ou Premium para Standard usando Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720950"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerenciado

Há quatro tipos de disco de discos gerenciados do Azure: ultra SSDs do Azure (visualização), SSD Premium, SSD padrão e HDD padrão. Você pode alternar entre os três tipos de disco de GA (SSD Premium, SSD padrão e HDD padrão) com base em suas necessidades de desempenho. Você ainda não é capaz de mudar de ou para um ultra SSD, você deve implantar um novo.

Não há suporte para essa funcionalidade em discos não gerenciados. Mas pode [facilmente converter um disco não gerido num disco gerido](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de disco.

 

## <a name="prerequisites"></a>Pré-requisitos

* Como a conversão requer uma reinicialização da VM (máquina virtual), você deve agendar a migração do armazenamento em disco durante uma janela de manutenção pré-existente.
* Se o disco não for gerido, [converta-o](convert-unmanaged-to-managed-disks.md) primeiro num disco gerido para que possa alternar entre opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Mudar todos os discos gerenciados de uma VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de uma VM do armazenamento Standard para Premium ou do armazenamento Premium para Standard. Para utilizar discos geridos premium, o seu VM deve utilizar um [tamanho VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também muda para um tamanho que dá suporte ao armazenamento Premium:

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos gerenciados individuais entre Standard e Premium

Para sua carga de trabalho de desenvolvimento/teste, talvez você queira uma combinação de discos Standard e Premium para reduzir os custos. Você pode optar por atualizar somente os discos que precisam de melhor desempenho. Este exemplo mostra como converter um único disco de VM do armazenamento Standard para Premium ou do armazenamento Premium para Standard. Para utilizar discos geridos premium, o seu VM deve utilizar um [tamanho VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que dá suporte ao armazenamento Premium:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Converter discos gerenciados do Standard para o Premium no portal do Azure

Siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de **máquinas Virtuais** no portal.
3. Se o VM não estiver parado, selecione **Stop** no topo do painel de **visão geral** vM e espere que o VM pare.
3. No painel para o VM, selecione **Discos** do menu.
4. Selecione o disco que você deseja converter.
5. **Selecione Configuração** do menu.
6. Alterar o tipo de **conta** de **HDD Standard** para **SSD Premium**.
7. Clique em **Guardar**e feche o painel do disco.

A conversão de tipo de disco é instantânea. Você pode iniciar a VM após a conversão.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Mudar os discos gerenciados entre HDD Standard e SSD Standard 

Este exemplo mostra como converter um único disco de VM de HDD Standard para SSD Standard ou de SSD Standard para HDD Standard:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passos Seguintes

Faça uma cópia apenas de um VM através de uma [imagem .](snapshot-copy-managed-disk.md)
