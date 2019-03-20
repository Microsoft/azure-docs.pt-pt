---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: c97c94492417dcc87d34751908f1766393ad37ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102557"
---
Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Desligar um disco remove o disco da máquina virtual, mas não elimina o disco da conta de armazenamento do Azure.

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.  

> [!NOTE]
> Para desligar um disco de sistema operativo, primeiro tem de eliminar a máquina virtual.
>

## <a name="find-the-disk"></a>Localizar o disco
Se não souber o nome do disco ou pretender verificar o mesmo antes de desligá-lo, siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Clique em **Máquinas Virtuais** e selecione a máquina virtual apropriada.

3. Clique em **Discos** ao longo da margem esquerda do dashboard de máquina virtual, em **Definições**.

   O dashboard de máquina virtual apresenta o nome e o tipo de todos os discos ligados. Por exemplo, este ecrã mostra uma máquina virtual com um disco de sistema operativo (SO) e um disco de dados:

    ![Localizar o disco de dados](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Desligar o disco
1. No portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique no nome da máquina virtual que tem o disco de dados que pretende desligar.

2. Clique em **Discos** ao longo da margem esquerda do dashboard de máquina virtual, em **Definições**.

3. Clique no disco que pretende desligar.

   ![Identificar o disco a desligar](./media/howto-detach-disk-windows-linux/disklist.png)

4. A partir da barra de comandos, clique em **Desligar**.

   ![Localizar o comando para desligar](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Na janela de confirmação, clique em **Sim** para desligar o disco.

   ![Confirmar a ação de desligar o disco](./media/howto-detach-disk-windows-linux/confirmdetach.png)

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.
