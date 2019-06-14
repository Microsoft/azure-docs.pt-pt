---
title: Anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 2e168867ed342fb0b0545b5fdc330ba790f78de0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60304518"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anexar ou desanexar um disco de dados a uma máquina virtual no Azure DevTest Labs
[Os Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) gerencia as contas de armazenamento associadas aos discos de dados de máquina virtual. Um anexa de utilizador do disco de um novos dados a uma VM, especifica o tipo e o tamanho do disco que é necessário, e o Azure cria e gere o disco automaticamente. O disco de dados, em seguida, pode ser desanexado da VM e a voltar a ligar mais tarde para a mesma VM ou ligada a uma VM diferente que pertença ao mesmo utilizador.

Esta funcionalidade é útil para gerir o armazenamento ou de software fora de cada máquina virtual individual. Se o armazenamento ou o software já existir dentro de um disco de dados, ele pode ser facilmente anexado, desanexado e voltar a ligar a qualquer VM que é propriedade do utilizador que é o proprietário desse disco de dados.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Antes de anexar um disco de dados a uma VM, reveja estas dicas:

- O tamanho da VM controla quantos discos de dados, pode anexar. Para obter detalhes, consulte [tamanhos de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Apenas pode anexar um disco de dados a uma VM que está em execução. Certificar-se de que a VM está em execução antes de tentar anexar um disco de dados.

### <a name="attach-a-new-disk"></a>Anexar um disco novo
Siga estes passos para criar e anexar um novo disco de dados geridos para uma VM no Azure DevTest Labs.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido. 
1. Na lista de **minhas máquinas virtuais**, selecione uma VM em execução.
1. No menu à esquerda, selecione **discos**.

    ![Selecione os discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Escolher **anexar novo** para criar um novo disco de dados e anexá-lo para a VM.

    ![Anexar novo disco de dados a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Concluir o **anexar novo disco** painel ao introduzir um nome de disco de dados, tipo e tamanho.

    ![Preencha o formulário de "anexar o disco novo"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecione **OK**.

Após alguns instantes, o novo disco de dados é criado e ligado à VM e é apresentada na lista de **discos de dados** para essa VM.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
Siga estes passos para religar um disco de dados disponíveis existente para uma VM em execução. 

1. Selecione uma VM em execução para o qual pretende voltar a ligá um disco de dados.
1. No menu à esquerda, selecione **discos**.
1. Selecione **anexar existente** para anexar um disco de dados disponíveis para a VM.

    ![Anexar disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Partir do **anexar disco existente** painel, selecione OK.

    ![Anexar disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Após alguns instantes, o disco de dados é anexado à VM e é apresentada na lista de **discos de dados** para essa VM.

## <a name="detach-a-data-disk"></a>Desanexar um disco de dados
Quando já não precisar de um disco de dados que está ligado a uma VM, pode desligá-lo facilmente. A desanexar remove o disco da VM, mas mantém-lo no armazenamento para utilização posterior.

Se pretender utilizar os dados existentes no disco, novamente, pode voltar a ligá-lo para a mesma máquina virtual ou para outro.

### <a name="detach-from-the-vms-management-pane"></a>Anular a exposição a partir do painel de gestão da VM
1. Na sua lista de máquinas virtuais, selecione uma VM que possui um disco de dados ligado.
1. No menu à esquerda, selecione **discos**.

    ![Selecione os discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Na lista de **discos de dados**, selecione o disco de dados que pretende desligar.
1. Selecione **anulação de exposições** da parte superior do painel de detalhes do disco.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecione **Sim** para confirmar que pretende desanexar o disco de dados.

O disco é desligado e está disponível para anexar a outra VM. 
### <a name="detach-from-the-labs-main-pane"></a>Desanexação do painel principal do laboratório
1. No painel principal do seu laboratório, selecione **Moje datové disky**.

    ![Aceder a discos de dados do laboratório](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Com o botão direito no disco de dados que pretende desanexar – ou selecione o botão de reticências (.....) – e escolha **anulação de exposições**.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecione **Sim** para confirmar que pretende desligá-lo.

   > [!NOTE]
   > Se um disco de dados já está desligado, é possível removê-lo da sua lista de discos de dados disponíveis, selecionando **eliminar**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Atualizar um disco de dados não gerido
Se tiver uma VM existente que utiliza discos de dados não gerido, pode converter facilmente a VM para utilizar discos geridos. Este processo converte o disco do SO e qualquer discos de dados anexados.

Para atualizar um disco de dados não geridos, siga os passos descritos neste artigo para [desanexar o disco de dados](#detach-a-data-disk) de uma VM não gerida. Em seguida, [o disco voltar a ligá](#attach-an-existing-disk) VM gerida para atualizar automaticamente os dados de disco de não gerido para gerido.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
Saiba como gerir discos de dados para [reclamáveis máquinas de virtuais](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

