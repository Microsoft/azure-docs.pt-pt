---
title: Desanexar um disco de dados de uma VM do Windows-Azure
description: Desanexar um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834610"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Windows

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isso remove o disco da máquina virtual, mas não o Remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você assinou o armazenamento Premium, continuará a incorrer em encargos de armazenamento para o disco. Para obter mais informações, consulte [preços e cobrança ao usar o armazenamento Premium](disks-types.md#billing).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.

 

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

Você pode *remover* um disco de dados usando o PowerShell, mas certifique-se de que nada esteja usando o disco ativamente antes de desanexá-lo da VM.

Neste exemplo, removemos o disco chamado **MyDisk** da VM **myVM** no grupo de recursos **MyResource** Group. Primeiro, remova o disco usando o cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Em seguida, você atualiza o estado da máquina virtual, usando o cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) , para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

Você pode *remover* um disco de dados, mas certifique-se de que nada esteja usando o disco ativamente antes de desanexá-lo da VM.

1. No menu à esquerda, selecione **máquinas virtuais**.
1. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar.
1. Em **Definições**, selecione **Discos**.
1. Na parte superior do painel **discos** , selecione **Editar**.
1. No painel **discos** , na extrema direita do disco de dados que você deseja desanexar, selecione **desanexar**.
1. Selecione **salvar** na parte superior da página para salvar as alterações.

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Se você quiser reutilizar o disco de dados, você pode apenas [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
