---
title: Desanexar um disco de dados de uma VM do Windows – Azure | Microsoft Docs
description: Desanexar um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: efbb6ccef9096ed89f6ccd16f8d3b37c9a97b278
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103218"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Windows

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isso remove o disco da máquina virtual, mas não o Remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você assinou o armazenamento Premium, continuará a incorrer em encargos de armazenamento para o disco. Para obter mais informações, consulte [preços e cobrança ao usar o armazenamento Premium](disks-types.md#billing).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

Você pode remover um disco de dados usando o PowerShell, mas certifique-se de que nada esteja usando o disco ativamente antes de desanexá-lo da VM.

Neste exemplo, removemos o disco chamado MyDisk da VM **myVM** no grupo de recursos **MyResource** Group. Primeiro, remova o disco usando o cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Em seguida, você atualiza o estado da máquina virtual, usando o cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) , para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

1. No menu à esquerda, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e clique em **parar** para desalocar a VM.
3. No painel máquina virtual, selecione **discos**.
4. Na parte superior do painel **discos** , selecione **Editar**.
5. No painel **discos** , na extrema direita do disco de dados que você deseja desanexar, clique no botão desanexar ![imagem](./media/detach-disk/detach.png) do botão desanexar.
5. Depois que o disco for removido, clique em **salvar** na parte superior do painel.
6. No painel máquina virtual, clique em **visão geral** e, em seguida, clique no botão **Iniciar** na parte superior do painel para reiniciar a VM.

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.

## <a name="next-steps"></a>Passos seguintes

Se você quiser reutilizar o disco de dados, você pode apenas [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)