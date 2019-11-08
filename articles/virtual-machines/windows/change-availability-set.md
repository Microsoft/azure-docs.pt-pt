---
title: Alterar um conjunto de disponibilidade de VMs | Microsoft Docs
description: Saiba como alterar o conjunto de disponibilidade para suas máquinas virtuais usando Azure PowerShell e o modelo de implantação do Gerenciador de recursos.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 5390643723f2907301554e69a1142cef181deb13
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749604"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Alterar o conjunto de disponibilidade para uma VM do Windows
As etapas a seguir descrevem como alterar o conjunto de disponibilidade de uma VM usando Azure PowerShell. Uma VM só pode ser adicionada a um conjunto de disponibilidade quando ela é criada. Para alterar o conjunto de disponibilidade, você precisa excluir e, em seguida, recriar a máquina virtual. 

Este artigo foi testado pela última vez em 2/12/2019 usando o [Azure cloud Shell](https://shell.azure.com/powershell) e o [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) versão 1.2.0.

 

## <a name="change-the-availability-set"></a>Alterar o conjunto de disponibilidade 

O script a seguir fornece um exemplo de como coletar as informações necessárias, excluir a VM original e, em seguida, recriá-la em um novo conjunto de disponibilidade.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Passos seguintes

Adicione armazenamento adicional à sua VM adicionando um disco de [dados](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)adicional.

