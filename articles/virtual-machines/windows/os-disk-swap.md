---
title: Trocar o disco do sistema operacional para uma VM do Azure com o PowerShell | Microsoft Docs '
description: Altere o disco do sistema operacional usado por uma máquina virtual do Azure usando o PowerShell.
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
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b213db38dade06e5015227494fa77b2f465ba1d9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089052"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Alterar o disco do sistema operacional usado por uma VM do Azure usando o PowerShell

Se você tiver uma VM existente, mas quiser trocar o disco por um disco de backup ou outro disco do sistema operacional, poderá usar Azure PowerShell para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM. Você pode até mesmo usar um disco gerenciado em outro grupo de recursos, desde que ele ainda não esteja em uso.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

A VM precisa ser stopped\deallocated, então a ID de recurso do disco gerenciado pode ser substituída pela ID de recurso de um disco gerenciado diferente.

Verifique se o tamanho da VM e o tipo de armazenamento são compatíveis com o disco que você deseja anexar. Por exemplo, se o disco que você deseja usar estiver no armazenamento Premium, a VM precisará ser capaz de armazenar o armazenamento Premium (como um tamanho da série DS). 

Obter uma lista de discos em um grupo de recursos usando [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando você tiver o nome do disco que deseja usar, defina-o como o disco do sistema operacional para a VM. Este exemplo stop\deallocates a VM denominada *myVM* e atribui o disco chamado *newDisk* como o novo disco do sistema operacional. 
 
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