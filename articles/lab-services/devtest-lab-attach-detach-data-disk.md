---
title: Anexar ou desmontar um disco de dados a uma máquina virtual em Laboratórios Azure DevTest
description: Aprenda a anexar ou desmontar um disco de dados a uma máquina virtual em Azure DevTest Labs
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
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198873"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anexar ou desmontar um disco de dados a uma máquina virtual em Laboratórios Azure DevTest
[A Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) gere as contas de armazenamento associadas aos discos de dados da máquina virtual. Um utilizador liga um novo disco de dados a um VM, especifica o tipo e o tamanho do disco que é necessário, e o Azure cria e gere o disco automaticamente. O disco de dados pode então ser separado do VM e religado mais tarde ao mesmo VM, ou ligado a um VM diferente que pertence ao mesmo utilizador.

Esta funcionalidade é útil para gerir armazenamento ou software fora de cada máquina virtual individual. Se o armazenamento ou software já existir dentro de um disco de dados, pode ser facilmente ligado, desconectado e religado a qualquer VM que seja propriedade do utilizador que detém esse disco de dados.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Antes de anexar um disco de dados a um VM, reveja estas dicas:

- O tamanho do VM controla quantos discos de dados pode anexar. Para mais detalhes, consulte [Tamanhos para máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Só é possível anexar um disco de dados a um VM que esteja em execução. Certifique-se de que o VM está a funcionar antes de tentar anexar um disco de dados.

### <a name="attach-a-new-disk"></a>Anexar um disco novo
Siga estes passos para criar e anexar um novo disco de dados gerido a um VM em Azure DevTest Labs.

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório desejado. 
1. A partir da lista das **Minhas máquinas virtuais,** selecione um VM em execução.
1. A partir do menu à esquerda, selecione **Discos**.
1. Escolha **anexar novo** para criar um novo disco de dados e anexá-lo ao VM.

    ![Anexar novo disco de dados a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Complete a nova vidraça do **disco,** inserindo um nome, tipo e tamanho do disco de dados.

    ![Complete o formulário "anexar novo disco"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecione **OK**.

Após alguns momentos, o novo disco de dados é criado e anexado ao VM e aparece na lista de **discos** data para esse VM.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
Siga estes passos para voltar a colocar um disco de dados disponível existente a um VM em execução. 

1. Selecione um VM em execução para o qual pretende voltar a colocar um disco de dados.
1. A partir do menu à esquerda, selecione **Discos**.
1. Selecione **anexar existente** para anexar um disco de dados disponível ao VM.

    ![Anexar o disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. A partir do painel de **disco existente,** selecione OK.

    ![Anexar o disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Após alguns momentos, o disco de dados é anexado ao VM e aparece na lista de discos de **Dados** para esse VM.

## <a name="detach-a-data-disk"></a>Desanexar um disco de dados
Quando já não precisa de um disco de dados ligado a um VM, pode facilmente desmontá-lo. A desvinculação remove o disco do VM, mas mantém-no guardado para ser utilizado mais tarde.

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-los à mesma máquina virtual ou a outra.

### <a name="detach-from-the-vms-management-pane"></a>Desprender-se do painel de gestão do VM
1. A partir da sua lista de máquinas virtuais, selecione um VM que tenha um disco de dados ligado.
1. A partir do menu à esquerda, selecione **Discos**.
1. A partir da lista de **discos data,** selecione o disco de dados que pretende desmontar.

    ![Selecione discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Selecione **Desmontar** a partir da parte superior dos detalhes do disco.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecione **Sim** para confirmar se pretende desmontar o disco de dados.

O disco está desconectado e está disponível para anexar a outro VM. 
### <a name="detach-from-the-labs-main-pane"></a>Desprender-se do painel principal do laboratório
1. No painel principal do seu laboratório, selecione os meus discos de **dados.**
1. Clique no disco de dados que pretende desprender – ou selecione a sua elipse (**...**) – e escolha **Desmontar**.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecione **Sim** para confirmar que pretende desmontá-lo.

   > [!NOTE]
   > Se um disco de dados já estiver separado, pode optar por removê-lo da sua lista de discos de dados disponíveis selecionando **Eliminar**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Atualizar um disco de dados não gerido
Se tiver um VM existente que utilize discos de dados não geridos, pode facilmente converter o VM para utilizar discos geridos. Este processo converte tanto o disco OS como quaisquer discos de dados anexados.

Para atualizar um disco de dados não gerido, siga os passos descritos neste artigo para [separar o disco](#detach-a-data-disk) de dados de um VM não gerido. Em seguida, [recolocar o disco](#attach-an-existing-disk) num VM gerido para atualizar automaticamente o disco de dados de não gerido para gerido.

## <a name="next-steps"></a>Passos seguintes
Saiba como gerir os discos de dados para [máquinas virtuais reiváveis](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

