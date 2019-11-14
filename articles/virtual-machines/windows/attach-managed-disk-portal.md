---
title: Anexar um disco de dados gerenciado a uma VM do Windows – Azure
description: Como anexar um disco de dados geridos para uma VM do Windows com o portal do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d98fdab6dcd0abb70967d1e17d138a860a31da5f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039305"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anexar um disco de dados geridos para uma VM do Windows com o portal do Azure

Este artigo mostra-lhe como anexar um novo disco de dados geridos para uma máquina virtual de Windows (VM) com o portal do Azure. O tamanho da VM determina quantos discos de dados, pode anexar. Para obter mais informações, consulte [tamanhos de máquinas virtuais](sizes.md).


## <a name="add-a-data-disk"></a>Adicionar um disco de dados

1. Na [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **máquinas virtuais**.
2. Selecione uma máquina virtual a partir da lista.
3. Sobre o **Máquina Virtual** página, selecione **discos**.
4. Sobre o **discos** página, selecione **adicionar disco de dados**.
5. Na lista pendente para o novo disco, selecione **criar disco**.
6. Na **disco gerido de criar** página, escreva um nome para o disco e ajustar as outras definições conforme necessário. Quando terminar, selecione **Criar**.
7. Na **discos** página, selecione **guardar** para guardar a nova configuração de disco para a VM.
8. Depois do Azure cria o disco e anexa-o para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligue à VM.
1. Selecione o Windows **começar** menu no interior da VM em execução e introduza **Diskmgmt. msc** na caixa de pesquisa. O **gestão de discos** consola é aberta.
2. Gestão de discos reconhece que tem um disco novo, não inicializado e o **Inicializar disco** é apresentada a janela.
3. Certifique-se de que o novo disco está selecionado e, em seguida, selecione **OK** para inicializá-la.
4. O novo disco aparece como **não alocado**. Com o botão direito em qualquer lugar no disco e selecione **novo volume simples**. O **Assistente de novo Volume simples** é aberta a janela.
5. Avance no assistente, manter todos os padrões, e quando terminar, selecione **concluir**.
6. Fechar **gestão de discos**.
7. É apresentada uma janela de pop-up a notificá-lo de que precisa de formatar o disco novo antes de poder utilizá-lo. Selecione **formatar disco**.
8. Na **Formatar novo disco** janela, verifique as definições e, em seguida, selecione **iniciar**.
9. É apresentado um aviso a informar de que os discos de formatação apague todos os dados. Selecione **OK**.
10. Quando a formatação estiver concluída, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

- Também pode [anexar um disco de dados com o PowerShell](attach-disk-ps.md).
- Se a sua aplicação precisa de utilizar o *D:* unidade para armazenar dados, pode [alterar a letra de unidade do disco temporário Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
