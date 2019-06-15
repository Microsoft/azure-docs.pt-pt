---
title: Desanexar um disco de dados de uma VM do Windows - Azure | Documentos da Microsoft
description: Desanexe um disco de dados de uma máquina virtual no Azure com o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 21b9ec78c4b4ca3a0f96bdd84be1d11e9d7257d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681860"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Windows

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isso remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se desligar um disco que não é eliminado automaticamente. Se tiver inscrito para o armazenamento Premium, continuará a incorrer em custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação quando utiliza o armazenamento Premium](disks-types.md#billing).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados com o PowerShell

Pode *frequente* remover um disco de dados com o PowerShell, mas certifique-se de que nada é ativamente a utilizar o disco antes de desligá-lo a partir da VM.

Neste exemplo, podemos remover o disco com o nome **myDisk** da VM **myVM** no **myResourceGroup** grupo de recursos. Primeiro de remover o disco com o [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) cmdlet. Em seguida, atualize o estado da máquina virtual, utilizando o [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) cmdlet, para concluir o processo de remover o disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

1. No menu da esquerda, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que pretende desanexar e clique em **parar** para anula a alocação da VM.
3. No painel da máquina virtual, selecione **discos**.
4. Na parte superior a **discos** painel, selecione **editar**.
5. Na **discos** painel, na extrema direita do disco de dados que pretende desanexar, clique nas ![imagem do botão de anulação de exposições](./media/detach-disk/detach.png) desanexar o botão.
5. Depois do disco tiver sido removido, clique em **guardar** na parte superior do painel.
6. No painel da máquina virtual, clique em **descrição geral** e, em seguida, clique nas **iniciar** botão na parte superior do painel para reiniciar a VM.

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.

## <a name="next-steps"></a>Passos Seguintes

Se quiser reutilizar o disco de dados, simplesmente [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)