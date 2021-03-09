---
title: Alterar um conjunto de disponibilidade de VMs usando Azure PowerShell
description: Saiba como alterar o conjunto de disponibilidade para a sua máquina virtual utilizando o Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: 99985d0bb2294c538efa712e477cc6f8a2eb4938
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498477"
---
# <a name="change-the-availability-set-for-a-vm-using-azure-powershell"></a>Alterar o conjunto de disponibilidade para um VM utilizando a Azure PowerShell    
Os seguintes passos descrevem como alterar o conjunto de disponibilidade de um VM utilizando a Azure PowerShell. Um VM só pode ser adicionado a um conjunto de disponibilidade quando é criado. Para alterar o conjunto de disponibilidade, é necessário eliminar e, em seguida, recriar a máquina virtual. 

Este artigo aplica-se tanto aos VMs Linux como ao Windows.

Este artigo foi testado pela última vez em 2/12/2019 utilizando a [Azure Cloud Shell](https://shell.azure.com/powershell) e a versão 1.2.0 do [módulo Az PowerShell.](/powershell/azure/install-az-ps)

Este exemplo não verifica se o VM está ligado a um equilibrador de carga. Se o seu VM estiver ligado a um equilibrador de carga, terá de atualizar o script para lidar com essa caixa. 


## <a name="change-the-availability-set"></a>Alterar o conjunto de disponibilidade 

O seguinte script fornece um exemplo de recolha das informações necessárias, eliminando o VM original e, em seguida, recriando-as em um novo conjunto de disponibilidade.

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

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
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
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
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

Adicione armazenamento adicional ao seu VM adicionando um disco de [dados](attach-managed-disk-portal.md)adicional.
