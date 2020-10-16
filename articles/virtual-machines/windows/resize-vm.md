---
title: Redimensionar um Windows VM em Azure
description: Mude o tamanho VM utilizado para uma máquina virtual Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: sizes
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 4860422fefb5a95fe41912b4898f02867f7b3aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87832251"
---
# <a name="resize-a-windows-vm"></a>Redimensionar VMs do Windows

Este artigo mostra-lhe como mover um VM para um [tamanho VM](../sizes.md)diferente .

Depois de criar uma máquina virtual (VM), pode escalar o VM para cima ou para baixo alterando o tamanho VM. Em alguns casos, primeiro tem de negociar com o VM. Isto pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que está atualmente hospedando o VM.

Se o seu VM utilizar o Premium Storage, certifique-se de que escolhe uma versão **s** do tamanho para obter suporte de Armazenamento Premium. Por exemplo, escolha Standard_E4**s**_v3 em vez de Standard_E4_v3.

## <a name="use-the-portal"></a>Utilizar o portal

1. Abra o [portal do Azure](https://portal.azure.com).
1. Abra a página para a máquina virtual.
1. No menu esquerdo, selecione **Tamanho**.
1. Escolha um novo tamanho na lista de tamanhos disponíveis e, em seguida, selecione **Resize**.


Se a máquina virtual estiver atualmente em funcionamento, a alteração do seu tamanho fará com que seja reiniciada. Parar a máquina virtual pode revelar tamanhos adicionais.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Use PowerShell para redimensionar um VM não num conjunto de disponibilidade

Definir algumas variáveis. Substitua os valores pela sua própria informação.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista os tamanhos VM que estão disponíveis no cluster de hardware onde o VM está hospedado. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho que pretende estiver listado, executar os seguintes comandos para redimensionar o VM. Se o tamanho desejado não estiver listado, continue para o passo 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Se o tamanho que deseja não estiver listado, executar os seguintes comandos para negociar o VM, redimensioná-lo e reiniciar o VM. **\<newVMsize>** Substitua-o pelo tamanho que pretende.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> A negociação do VM lança quaisquer endereços IP dinâmicos atribuídos ao VM. O SO e os discos de dados não são afetados. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Use PowerShell para redimensionar um VM num conjunto de disponibilidade

Se o novo tamanho para um VM num conjunto de disponibilidade não estiver disponível no cluster de hardware que atualmente alberga o VM, então todos os VMs no conjunto de disponibilidade terão de ser translocados para redimensionar o VM. Também poderá ser necessário atualizar o tamanho de outros VMs no conjunto de disponibilidade depois de um VM ter sido redimensionado. Para redimensionar um VM num conjunto de disponibilidade, execute os seguintes passos.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista os tamanhos VM que estão disponíveis no cluster de hardware onde o VM está hospedado. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se o tamanho desejado for listado, executar os seguintes comandos para redimensionar o VM. Se não estiver listado, vá para a secção seguinte.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se o tamanho que deseja não estiver listado, continue com os seguintes passos para transcolocar todos os VMs no conjunto de disponibilidade, redimensionar VMs e reiniciá-los.

Pare todos os VMs no conjunto de disponibilidade.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Redimensione e reinicie os VMs no conjunto de disponibilidade.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>Passos seguintes

Para uma escalabilidade adicional, executar vários casos de VM e escalar para fora. Para obter mais informações, consulte [automaticamente as máquinas windows num conjunto de escala de máquina virtual](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).