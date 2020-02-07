---
title: Anexar um disco de dados gerenciado a uma VM do Windows – Azure
description: Como anexar um disco de dados geridos para uma VM do Windows com o portal do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8b651d6e5ca9262d38f5bfb9e10c404e2614809e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77055673"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anexar um disco de dados geridos para uma VM do Windows com o portal do Azure

Este artigo mostra-lhe como anexar um novo disco de dados geridos para uma máquina virtual de Windows (VM) com o portal do Azure. O tamanho da VM determina quantos discos de dados, pode anexar. Para mais informações, consulte [Tamanhos para máquinas virtuais](sizes.md).


## <a name="add-a-data-disk"></a>Adicionar um disco de dados

1. Vá ao [portal Azure](https://portal.azure.com) para adicionar um disco de dados. Procure e selecione **máquinas Virtuais**.
2. Selecione uma máquina virtual a partir da lista.
3. Na página da **máquina Virtual,** selecione **Discos**.
4. Na página **De Discos,** selecione **Adicionar disco de dados**.
5. Na gota para o novo disco, selecione **Criar disco**.
6. Na página de **disco gerida** create, digite um nome para o disco e ajuste as outras definições conforme necessário. Quando terminar, selecione **Criar**.
7. Na página **De Discos,** selecione **Guardar** para guardar a nova configuração do disco para o VM.
8. Depois de o Azure criar o disco e ligá-lo à máquina virtual, o novo disco está listado nas definições do disco da máquina virtual sob **os discos data**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligue à VM.
1. Selecione o menu Windows **Iniciar** dentro do VM em execução e introduza **diskmgmt.msc** na caixa de pesquisa. A consola **de Gestão de Discos** abre.
2. A Disk Management reconhece que tem um disco novo e não inicializado e aparece a janela **do Disco Inicialize.**
3. Verifique se o novo disco está selecionado e, em seguida, selecione **OK** para inicializá-lo.
4. O novo disco aparece como **não atribuído**. Clique à direita em qualquer lugar do disco e selecione **Novo volume simples**. A janela **do Novo Assistente** de Volume Simples abre.
5. Proceda através do assistente, mantendo todos os predefinições, e quando terminar, selecione **Finish**.
6. Fechar **a Gestão do Disco.**
7. É apresentada uma janela de pop-up a notificá-lo de que precisa de formatar o disco novo antes de poder utilizá-lo. Selecione **o disco de formato**.
8. Na nova janela do **formato,** verifique as definições e, em seguida, selecione **Iniciar**.
9. É apresentado um aviso a informar de que os discos de formatação apague todos os dados. Selecione **OK**.
10. Quando a formatação estiver completa, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

- Também pode anexar um disco de [dados utilizando powerShell](attach-disk-ps.md).
- Se a sua aplicação necessitar de utilizar o *D:* dirija para armazenar dados, pode [alterar a letra de unidade do disco temporário windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
