---
title: Retire um disco de dados de um Windows VM - Azure
description: Retire um disco de dados de uma máquina virtual em Azure utilizando o modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 02192850dd9103b6c1783baf42c184ee16fe299f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86500689"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Windows

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isto remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se separar um disco, não é apagado automaticamente. Se subscrever o armazenamento Premium, continuará a incorrer em custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação ao utilizar o Premium Storage](disks-types.md#billing).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.

 

## <a name="detach-a-data-disk-using-powershell"></a>Descoser um disco de dados usando o PowerShell

Pode remover um disco de dados *a quente* usando o PowerShell, mas certifique-se de que nada está a utilizar ativamente o disco antes de o desvincular do VM.

Neste exemplo, removemos o disco chamado **myDisk** do **myVM** VM no grupo de recursos **myResourceGroup.** Primeiro, remova o disco utilizando o [cmdlet Remove-AzVMDataDisk.](/powershell/module/az.compute/remove-azvmdatadisk) Em seguida, atualiza o estado da máquina virtual, utilizando o cmdlet [Update-AzVM,](/powershell/module/az.compute/update-azvm) para completar o processo de remoção do disco de dados.

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

O disco permanece no armazenamento mas já não está ligado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

Pode remover um disco de dados *a quente,* mas certifique-se de que nada está a utilizar ativamente o disco antes de o desvincular do VM.

1. No menu esquerdo, selecione **Máquinas Virtuais.**
1. Selecione a máquina virtual que tem o disco de dados que pretende desmontar.
1. Em **Definições**, selecione **Discos**.
1. Na parte superior do **painel de discos,** selecione **Editar**.
1. No **painel Discos,** à extrema direita do disco de dados que gostaria de desmontar, selecione **Detach**.
1. **Selecione Guardar** na parte superior da página para guardar as suas alterações.

O disco permanece no armazenamento mas já não está ligado a uma máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Se quiser reutilizar o disco de dados, pode [anexá-lo a outro VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
