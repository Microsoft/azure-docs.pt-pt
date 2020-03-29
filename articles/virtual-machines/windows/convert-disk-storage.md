---
title: Converter armazenamento de discos geridos entre sSD standard e premium
description: Como converter discos geridos pelo Azure da Standard para Premium ou Premium para standard utilizando o Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720950"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerido

Existem quatro tipos de discos geridos pelo Azure: Azure ultra SSDs (pré-visualização), SSD premium, SSD padrão e HDD padrão. Pode alternar entre os três tipos de disco GA (SSD premium, SSD padrão e HDD padrão) com base nas suas necessidades de desempenho. Ainda não é capaz de mudar de ou para um Ultra SSD, tem de implementar um novo.

Esta funcionalidade não é suportada para discos não geridos. Mas pode [facilmente converter um disco não gerido num disco gerido](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de disco.

 

## <a name="prerequisites"></a>Pré-requisitos

* Uma vez que a conversão requer um reinício da máquina virtual (VM), deverá agendar a migração do seu armazenamento de disco durante uma janela de manutenção pré-existente.
* Se o disco não for gerido, [converta-o](convert-unmanaged-to-managed-disks.md) primeiro num disco gerido para que possa alternar entre opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Altere todos os discos geridos de um VM entre Premium e Standard

Este exemplo mostra como converter todos os discos de um VM de armazenamento Standard para Premium ou de Premium para armazenamento Standard. Para utilizar discos geridos premium, o seu VM deve utilizar um [tamanho VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também muda para um tamanho que suporta armazenamento premium:

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Alternar discos geridos individuais entre Standard e Premium

Para a sua carga de trabalho de v/teste, pode querer uma mistura de discos Standard e Premium para reduzir os seus custos. Pode optar por atualizar apenas os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM da Standard para o armazenamento Premium ou de Armazenamento Premium para Standard. Para utilizar discos geridos premium, o seu VM deve utilizar um [tamanho VM](sizes.md) que suporta o armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que suporta o armazenamento Premium:

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Converter discos geridos da Standard para Premium no portal Azure

Siga estes passos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de **máquinas Virtuais** no portal.
3. Se o VM não estiver parado, selecione **Stop** no topo do painel de **visão geral** vM e espere que o VM pare.
3. No painel para o VM, selecione **Discos** do menu.
4. Selecione o disco que pretende converter.
5. **Selecione Configuração** do menu.
6. Alterar o tipo de **conta** de **HDD Standard** para **SSD Premium**.
7. Clique em **Guardar**e feche o painel do disco.

A conversão do tipo disco é instantânea. Pode iniciar o seu VM após a conversão.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Switch gerido discos entre Standard HDD e Standard SSD 

Este exemplo mostra como converter um único disco VM de Standard HDD para Standard SSD ou de Standard SSD para Standard HDD:

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

## <a name="next-steps"></a>Passos seguintes

Faça uma cópia apenas de um VM através de uma [imagem .](snapshot-copy-managed-disk.md)
