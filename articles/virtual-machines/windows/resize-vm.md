---
title: Redimensionar um VM windows em Azure
description: Altere o tamanho VM utilizado para uma máquina virtual Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941727"
---
# <a name="resize-a-windows-vm"></a>Redimensionar VMs do Windows

Este artigo mostra-lhe como mover um VM para um [tamanho VM](sizes.md)diferente .

Depois de criar uma máquina virtual (VM), pode escalar o VM para cima ou para baixo alterando o tamanho VM. Em alguns casos, primeiro deve desalocar o VM. Isto pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que está atualmente a acolher o VM.

Se o seu VM utilizar o Armazenamento Premium, certifique-se de que escolhe uma versão **S** do tamanho para obter suporte de Armazenamento Premium. Por exemplo, escolha Standard_E4 **_v3**em vez de Standard_E4_v3.

## <a name="use-the-portal"></a>Utilizar o portal

1. Abra o [portal Azure.](https://portal.azure.com)
1. Abra a página para a máquina virtual.
1. No menu esquerdo, selecione **Tamanho**.
1. Escolha um novo tamanho da lista de tamanhos disponíveis e, em seguida, **selecione Resize**.


Se a máquina virtual estiver atualmente em funcionamento, a alteração do seu tamanho fará com que seja reiniciada. Parar a máquina virtual pode revelar tamanhos adicionais.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Use powerShell para redimensionar um VM não em um conjunto de disponibilidade

Definir algumas variáveis. Substitua os valores com a sua própria informação.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Enumera rine os tamanhos vm que estão disponíveis no cluster de hardware onde o VM está hospedado. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho que deseja estiver listado, execute os seguintes comandos para redimensionar o VM. Se o tamanho desejado não estiver listado, vá para o passo 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Se o tamanho que deseja não estiver listado, execute os seguintes comandos para desalojar o VM, redimensioná-lo e reiniciar o VM. Substitua os ** \<novos>vVMsize** pelo tamanho que deseja.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A locação do VM liberta quaisquer endereços IP dinâmicos atribuídos ao VM. O Sistema operativo e os discos de dados não são afetados. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Use powerShell para redimensionar um VM em um conjunto de disponibilidade

Se o novo tamanho para um VM num conjunto de disponibilidade não estiver disponível no cluster de hardware atualmente hospedado do VM, então todos os VMs no conjunto de disponibilidade terão de ser transferidos para redimensionar o VM. Também poderá necessitar de atualizar o tamanho de outros VMs no conjunto de disponibilidade após a redimensionação de um VM. Para redimensionar um VM num conjunto de disponibilidade, execute os seguintes passos.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Enumera rine os tamanhos vm que estão disponíveis no cluster de hardware onde o VM está hospedado. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado estiver listado, execute os seguintes comandos para redimensionar o VM. Se não estiver listado, vá para a próxima secção.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se o tamanho que deseja não estiver listado, continue com os seguintes passos para desalojar todos os VMs no conjunto de disponibilidade, redimensionar VMs e reiniciá-los.

Pare todos os VMs no conjunto de disponibilidade.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Redimensione e reinicie os VMs no conjunto de disponibilidade.
   
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

Para maior escalabilidade, executar várias instâncias vm e escalar para fora. Para mais informações, consulte [automaticamente as máquinas do Windows num conjunto](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)de escala de máquina virtual .

