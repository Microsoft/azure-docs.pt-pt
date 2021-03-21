---
title: Troque o disco DE por um Azure VM com PowerShell '
description: Altere o disco do sistema operativo utilizado por uma máquina virtual Azure utilizando o PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 8e928944a7508cc2a0ed35e89189fa2dd8c50665
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550387"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Alterar o disco OS utilizado por um Azure VM utilizando o PowerShell

Se tiver um VM existente, mas pretender trocar o disco por um disco de backup ou outro disco DE, pode utilizar o Azure PowerShell para trocar os discos OS. Não é preciso apagar e recriar o VM. Pode até utilizar um disco gerido noutro grupo de recursos, desde que ainda não esteja a ser utilizado.

 

O VM precisa de ser interrompido\deallocated, em seguida, o ID de recurso do disco gerido pode ser substituído pelo ID de recurso de um disco gerido diferente.

Certifique-se de que o tamanho e o tipo de armazenamento VM são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar estiver no Armazenamento Premium, então o VM tem de ser capaz de armazenamento Premium (como um tamanho da série DS). Ambos os discos também devem ter o mesmo tamanho.
E certifique-se de que não está a misturar um VM não encriptado com um disco de OS encriptado, isto não é suportado. Se o VM não utilizar a encriptação do disco Azure, então o disco OS que está a ser trocado não deve estar a utilizar a Encriptação do Disco Azure.

Obtenha uma lista de discos em um grupo de recursos usando [Get-AzDisk](/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando tiver o nome do disco que gostaria de utilizar, desajuste-o como o disco de so para o VM. Este exemplo stop\dealloca o VM nomeado *myVM* e atribui o disco nomeado *newDisk* como o novo disco OS. 
 
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

**Próximos passos**

Para criar uma cópia de um disco, consulte [Snapshot um disco](snapshot-copy-managed-disk.md).
