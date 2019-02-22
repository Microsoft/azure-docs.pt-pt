---
title: Converter os Azure managed armazenamento de discos de standard para premium e vice-versa | Documentos da Microsoft
description: Como converter do Azure gerido discos de standard para premium e vice-versa, com o Azure PowerShell.
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
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 14b6559420fad22cfc2294817cbefd23a6c47a70
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650109"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerido

Azure managed disks oferece quatro tipo as opções de armazenamento: Unidades de ultra de estado sólido (SSD), o Premium SSD, o Standard SSD e o padrão unidades de disco rígido (HDD). Pode optar por um disco gerido entre tipos de armazenamento com o tempo de inatividade mínimo, com base nas suas necessidades de desempenho. Alternar entre tipos de armazenamento não é suportada para um disco não gerido; No entanto, pode facilmente [converter um disco não gerido para um disco gerido](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma vez que a conversão necessita de um reinício da máquina virtual (VM), deve agendar a migração do armazenamento de discos durante uma janela de manutenção pré-existente. 
* Se estiver a utilizar um disco não gerido, comece [convertê-lo para um disco gerido](convert-unmanaged-to-managed-disks.md) para que possa alterá-la entre os tipos de armazenamento. 
* Os exemplos neste artigo requerem o Azure PowerShell versão do módulo 6.0.0 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps). Execute [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) para criar uma ligação com o Azure.

* Os exemplos neste artigo requerem o Azure PowerShell versão do módulo 6.0.0 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps). Execute [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) para criar uma ligação com o Azure.

## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Converter todos os discos geridos de uma VM de standard para premium

O exemplo seguinte mostra como mudar todos os discos de uma VM de standard para o armazenamento premium. Para utilizar discos geridos premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento premium. Este exemplo também muda para um tamanho que suporte o armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
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

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Converter um disco gerido de standard para premium

Para sua carga de trabalho de programador/teste, poderá ter uma mistura de discos standard e premium para reduzir o custo. Para tal, atualize para o armazenamento premium só os discos que exigem um melhor desempenho. O exemplo seguinte mostra como alternar de um único disco de uma VM do plano padrão para o armazenamento premium e vice-versa. Para utilizar discos geridos premium, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporta o armazenamento premium. Este exemplo também mostra como mudar para um tamanho que suporte o armazenamento premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-azure-portal"></a>Converter os discos geridos standard para premium no portal do Azure

Pode converter um disco gerido de standard para premium no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM a partir da lista de **máquinas virtuais** no portal.
3. Se a VM não estiver parada, clique em **parar** na parte superior do painel de descrição geral da VM e aguarde que a VM parar.
3. No painel da VM, selecione **discos** no menu.
4. Selecione o disco que pretende converter.
5. Selecione **configuração** no menu.
6. Alterar o **tipo de conta** partir **Standard HDD** para **Premium SSD**.
7. Clique em **guardar** e feche o painel de disco.

A atualização do tipo de disco está em vigor instantânea. Pode reiniciar a VM após a conversão.

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Converter um disco gerido standard HDD para standard SSD

O exemplo seguinte mostra como alternar de um único disco de uma VM do padrão HDD para SSD standard e vice-versa:

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

