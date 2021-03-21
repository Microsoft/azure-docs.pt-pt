---
title: Anexar um disco de dados gerido a um Windows VM - Azure
description: Como anexar um disco de dados gerido a um VM do Windows utilizando o portal Azure.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8c64b0ff5b7a9abfa58ec17d0ebcabe05b0ed6e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550812"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anexar um disco de dados gerido a um Windows VM utilizando o portal Azure

Este artigo mostra-lhe como anexar um novo disco de dados gerido a uma máquina virtual do Windows (VM) utilizando o portal Azure. O tamanho do VM determina quantos discos de dados pode anexar. Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../sizes.md)


## <a name="add-a-data-disk"></a>Adicionar um disco de dados

1. Vá ao [portal Azure](https://portal.azure.com) para adicionar um disco de dados. Procure e selecione **máquinas Virtuais.**
2. Selecione uma máquina virtual da lista.
3. Na página **da máquina Virtual,** selecione **Discos**.
4. Na página **Discos,** **selecione Adicionar o disco de dados**.
5. No drop-down para o novo disco, **selecione Criar o disco**.
6. Na página de **disco gerida Create,** digite um nome para o disco e ajuste as outras definições conforme necessário. Quando concluir, selecione **Criar**.
7. Na página **Discos,** **selecione Guardar** para guardar a nova configuração do disco para o VM.
8. Depois de o Azure criar o disco e o ligar à máquina virtual, o novo disco está listado nas definições de disco da máquina virtual nos **discos de dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Ligue à VM.
1. Selecione o menu **Iniciar** do Windows dentro do VM em execução e introduza **diskmgmt.msc** na caixa de pesquisa. A consola **de Gestão de Discos** abre.
2. A Disk Management reconhece que tem um novo disco não iniciado e aparece a janela **do Disco Inicialize.**
3. Verifique se o novo disco está selecionado e, em seguida, selecione **OK** para inicializá-lo.
4. O novo disco aparece como **não atribuído.** Clique com o botão direito em qualquer lugar do disco e selecione **Novo volume simples**. Abre-se a nova janela **do Assistente de Volume Simples.**
5. Proceda através do assistente, mantendo todos os predefinidos, e quando terminar, **selecione Terminar**.
6. Gestão de **Discos Fechados**.
7. Uma janela pop-up aparece a notificar-lhe que precisa de formatar o novo disco antes de o poder utilizar. Selecione **o disco de formato**.
8. Na nova janela do **disco formato,** verifique as definições e, em seguida, selecione **Iniciar**.
9. Aparece um aviso a notificar-lhe que a formatação dos discos apaga todos os dados. Selecione **OK**.
10. Quando a formatação estiver completa, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

- Também pode [anexar um disco de dados utilizando o PowerShell](attach-disk-ps.md).
- Se a sua aplicação precisar de utilizar o *D:* conduzir para armazenar dados, pode [alterar a letra de unidade do disco temporário do Windows.](change-drive-letter.md)