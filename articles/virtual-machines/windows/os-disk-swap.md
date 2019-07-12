---
title: Disco de SO de troca para uma VM do Azure com o PowerShell | Documentos da Microsoft
description: Altere o disco do sistema operativo utilizado por uma máquina virtual do Azure com o PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ae3979f7ceae4a854df00b39d9c2b9673f65f987
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720128"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Alterar o disco do SO utilizado por uma VM do Azure com o PowerShell

Se tiver uma VM existente, mas pretende trocar o disco para um disco de cópia de segurança ou de outro disco do SO, pode utilizar o Azure PowerShell para trocar os discos de SO. Não tem de eliminar e recriar a VM. Pode usar um disco gerido outro grupo de recursos,, desde que ainda não estiver em utilização.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

A VM tem de ser stopped\deallocated, em seguida, o ID de recurso do disco gerido pode ser substituído com o ID de recurso de um disco gerido diferente.

Certifique-se de que o tipo de armazenamento e tamanho VM são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar no armazenamento Premium, em seguida, a VM tem de ter a capacidade de armazenamento Premium (como um tamanho de séries de DS). 

Obter uma lista de discos num grupo de recursos utilizando [Get AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando tiver o nome do disco que pretende utilizar, defina-a como o disco do SO para a VM. Este exemplo stop\deallocates VM com o nome *myVM* e atribui o disco com o nome *newDisk* como o novo disco de SO. 
 
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

**Passos seguintes?**

Para criar uma cópia de um disco, consulte [instantâneo de um disco](snapshot-copy-managed-disk.md).