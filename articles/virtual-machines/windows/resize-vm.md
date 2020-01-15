---
title: Redimensionar uma VM do Windows no Azure
description: Alterar o tamanho da VM usado para uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941727"
---
# <a name="resize-a-windows-vm"></a>Redimensionar uma VM do Windows

Este artigo mostra como mover uma VM para um [tamanho de VM](sizes.md)diferente.

Depois de criar uma VM (máquina virtual), você pode escalar ou reduzir verticalmente a VM alterando o tamanho da VM. Em alguns casos, você deve desalocar a VM primeiro. Isso pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que está hospedando a VM no momento.

Se sua VM usar o armazenamento Premium, certifique-se de escolher uma versão de **s** do tamanho para obter suporte de armazenamento Premium. Por exemplo, escolha Standard_E4**s**_v3 em vez de Standard_E4_v3.

## <a name="use-the-portal"></a>Utilizar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Abra a página da máquina virtual.
1. No menu à esquerda, selecione **tamanho**.
1. Escolha um novo tamanho na lista de tamanhos disponíveis e, em seguida, selecione **redimensionar**.


Se a máquina virtual estiver em execução no momento, alterar seu tamanho fará com que ela seja reiniciada. Parar a máquina virtual pode revelar tamanhos adicionais.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Usar o PowerShell para redimensionar uma VM que não está em um conjunto de disponibilidade

Defina algumas variáveis. Substitua os valores por suas próprias informações.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos de VM que estão disponíveis no cluster de hardware onde a VM está hospedada. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado estiver listado, execute os comandos a seguir para redimensionar a VM. Se o tamanho desejado não estiver listado, vá para a etapa 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Se o tamanho desejado não estiver listado, execute os comandos a seguir para desalocar a VM, redimensioná-la e reiniciar a VM. Substitua **\<newVMsize >** pelo tamanho desejado.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A desalocação da VM libera todos os endereços IP dinâmicos atribuídos à VM. O sistema operacional e os discos de dados não são afetados. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Usar o PowerShell para redimensionar uma VM em um conjunto de disponibilidade

Se o novo tamanho de uma VM em um conjunto de disponibilidade não estiver disponível no cluster de hardware que atualmente hospeda a VM, todas as VMs no conjunto de disponibilidade precisarão ser desalocadas para redimensionar a VM. Você também pode precisar atualizar o tamanho de outras VMs no conjunto de disponibilidade depois que uma VM for redimensionada. Para redimensionar uma VM em um conjunto de disponibilidade, execute as etapas a seguir.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste os tamanhos de VM que estão disponíveis no cluster de hardware onde a VM está hospedada. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado estiver listado, execute os comandos a seguir para redimensionar a VM. Se não estiver listado, vá para a próxima seção.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se o tamanho desejado não estiver listado, continue com as etapas a seguir para desalocar todas as VMs no conjunto de disponibilidade, redimensionar as VMs e reiniciá-las.

Interrompa todas as VMs no conjunto de disponibilidade.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Redimensione e reinicie as VMs no conjunto de disponibilidade.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Passos seguintes

Para obter escalabilidade adicional, execute várias instâncias de VM e expanda horizontalmente. Para obter mais informações, consulte [dimensionar automaticamente máquinas do Windows em um conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

