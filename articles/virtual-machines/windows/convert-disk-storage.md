---
title: Converter armazenamento de discos geridos entre diferentes tipos de discos utilizando a Azure PowerShell
description: Como converter discos geridos pela Azure entre os diferentes tipos de discos utilizando o Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 1d1c191c746d6853f922302d74c6eefcba547f80
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519750"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerido

Existem quatro tipos de discos geridos azure: discos ultra Azure, SSD premium, SSD padrão e HDD padrão. Pode alternar entre SSD premium, SSD padrão e HDD padrão com base nas suas necessidades de desempenho. Ainda não é capaz de mudar de ou para um disco ultra, tem de implementar um novo.

Esta funcionalidade não é suportada por discos não geridos. Mas pode [facilmente converter um disco não gerido para um disco gerido](convert-unmanaged-to-managed-disks.md) para poder alternar entre tipos de discos.



## <a name="before-you-begin"></a>Antes de começar

* Uma vez que a conversão requer um reinício da máquina virtual (VM), deve agendar a migração do seu armazenamento em disco durante uma janela de manutenção pré-existente.
* Se o disco não for gerido, [converta-o](convert-unmanaged-to-managed-disks.md) primeiro num disco gerido para que possa alternar entre as opções de armazenamento.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Altere todos os discos geridos de um VM entre uma conta para outra

Este exemplo mostra como converter todos os discos de um VM para armazenamento premium. No entanto, alterando a variável $storageType neste exemplo, pode converter o tipo de discos VM para SSD padrão ou HDD padrão. Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também muda para um tamanho que suporta armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Altere discos geridos individuais entre Standard e Premium

Para a sua carga de trabalho de dev/teste, pode querer uma mistura de discos Standard e Premium para reduzir os seus custos. Pode optar por atualizar apenas os discos que precisam de um melhor desempenho. Este exemplo mostra como converter um único disco VM do armazenamento Standard para Premium. No entanto, alterando a variável $storageType neste exemplo, pode converter o tipo de discos VM para SSD padrão ou HDD padrão. Para utilizar discos geridos Premium, o seu VM deve utilizar um [tamanho VM](../sizes.md) que suporte o armazenamento Premium. Este exemplo também mostra como mudar para um tamanho que suporta armazenamento Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

A conversão do tipo disco é instantânea. Pode iniciar o seu VM após a conversão.

## <a name="next-steps"></a>Passos seguintes

Faça uma cópia apenas de leitura de um VM utilizando uma [foto instantânea](snapshot-copy-managed-disk.md).