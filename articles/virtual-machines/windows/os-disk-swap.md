---
title: Troque o disco OS por um VM Azure com PowerShell '
description: Altere o disco do sistema operativo utilizado por uma máquina virtual Azure utilizando o PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869375"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Alterar o disco OS utilizado por um VM Azure utilizando a PowerShell

Se tiver um VM existente, mas pretender trocar o disco por um disco de reserva ou outro disco OPERATIVO, pode utilizar o Azure PowerShell para trocar os discos OS. Não é preciso apagar e recriar o VM. Pode até utilizar um disco gerido noutro grupo de recursos, desde que ainda não esteja em uso.

 

O VM precisa de ser interrompido\deallocated, então o ID de recursos do disco gerido pode ser substituído com o ID de recurso de um disco gerido diferente.

Certifique-se de que o tamanho VM e o tipo de armazenamento são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar estiver no Armazenamento Premium, então o VM precisa de ser capaz de armazenamento premium (como um tamanho da série DS). Ambos os discos também devem ter o mesmo tamanho.

Obtenha uma lista de discos em um grupo de recursos usando [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando tiver o nome do disco que gostaria de utilizar, detetetete-o como o disco OS para o VM. Este exemplo stop\deallocates o VM nomeado *myVM* e atribui o disco nomeado *newDisk* como o novo disco OS. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Passos seguintes**

Para criar uma cópia de um disco, consulte [snapshot um disco](snapshot-copy-managed-disk.md).
