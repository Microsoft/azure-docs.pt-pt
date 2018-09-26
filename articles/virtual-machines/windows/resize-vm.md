---
title: Utilizar o PowerShell para redimensionar uma VM do Windows no Azure | Documentos da Microsoft
description: Redimensione uma máquina de virtual do Windows criada no modelo de implementação do Resource Manager, com o Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 6bd41115f586bf2969dacb772f097d84654f0306
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092599"
---
# <a name="resize-a-windows-vm"></a>Redimensionar um VM do Windows

Este artigo mostra-lhe como mover uma VM para outra [tamanho da VM](sizes.md) com o Azure Powershell.

Depois de criar uma máquina virtual (VM), pode dimensionar a VM ou reduzir verticalmente ao alterar o tamanho da VM. Em alguns casos, deve desalocar a VM pela primeira vez. Isto pode acontecer se o novo tamanho não está disponível no cluster de hardware que está atualmente a alojar a VM.

Se a VM utiliza o armazenamento Premium, certifique-se de que escolher uma **s** versão do tamanho para obter suporte de armazenamento Premium. Por exemplo, escolher Standard_E4**s**_v3 em vez de Standard_E4_v3.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionar uma VM do Windows não está no conjunto de disponibilidade

Defina algumas variáveis. Substitua os valores com as suas próprias informações.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos VM que estão disponíveis no cluster de hardware em que a VM está alojada. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho pretendido estiver listado, execute os seguintes comandos para redimensionar a VM. Se o tamanho pretendido não estiver listado, avance para o passo 3.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Se o tamanho pretendido não estiver listado, execute os seguintes comandos para desalocar a VM, redimensione-o e reinicie a VM. Substitua **<newVMsize>** com o tamanho pretendido.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A desalocar a VM versões quaisquer endereços IP dinâmicos atribuídos à VM. Os discos de SO e dados não são afetados. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionar uma VM do Windows num conjunto de disponibilidade

Se o novo tamanho de uma VM num conjunto de disponibilidade não está disponível no cluster de hardware atualmente a alojar a VM, em seguida, todas as VMs no conjunto de disponibilidade terá de ser desalocada para redimensionar a VM. Também poderá ter de atualizar o tamanho de outras VMs no conjunto depois de uma VM foi redimensionada de disponibilidade. Redimensionar uma VM num conjunto de disponibilidade, execute os seguintes passos.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos VM que estão disponíveis no cluster de hardware em que a VM está alojada. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho pretendido estiver listado, execute os seguintes comandos para redimensionar a VM. Se não estiver na lista, vá para a secção seguinte.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se o tamanho pretendido não estiver listado, continue com os seguintes passos para desalocar todas as VMs no conjunto de disponibilidade, redimensionar VMs e reiniciá-las.

Pare todas as VMs no conjunto de disponibilidade.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Redimensionar e reiniciar as VMs no conjunto de disponibilidade.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Passos Seguintes

Para escalabilidade adicional, execute várias instâncias VM e aumentar horizontalmente. Para obter mais informações, consulte [Dimensionar automaticamente máquinas do Windows num conjunto de dimensionamento de Máquina Virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

