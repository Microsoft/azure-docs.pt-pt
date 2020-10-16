---
title: Anexar ou separar um disco de dados a uma máquina virtual em Azure DevTest Labs
description: Saiba como anexar ou desprender um disco de dados a uma máquina virtual em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8eebfbda421233bcec780d441a4020acce740618
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328518"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anexar ou separar um disco de dados a uma máquina virtual em Azure DevTest Labs
[A Azure Managed Disks](../virtual-machines/managed-disks-overview.md) gere as contas de armazenamento associadas a discos de dados de máquinas virtuais. Um utilizador anexa um novo disco de dados a um VM, especifica o tipo e tamanho do disco que é necessário, e o Azure cria e gere o disco automaticamente. O disco de dados pode então ser desligado do VM e recolocado mais tarde no mesmo VM, ou ligado a um VM diferente que pertence ao mesmo utilizador.

Esta funcionalidade é útil para gerir armazenamento ou software fora de cada máquina virtual individual. Se o armazenamento ou software já existir dentro de um disco de dados, pode ser facilmente ligado, destacado e religado a qualquer VM que seja propriedade do utilizador que detém esse disco de dados.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Antes de anexar um disco de dados a um VM, reveja estas dicas:

- O tamanho do VM controla quantos discos de dados pode anexar. Para mais detalhes, consulte [tamanhos para máquinas virtuais.](../virtual-machines/sizes.md)
- Só é possível anexar um disco de dados a um VM que esteja em execução. Certifique-se de que o VM está em funcionamento antes de tentar anexar um disco de dados.

### <a name="attach-a-new-disk"></a>Anexar um disco novo
Siga estes passos para criar e anexar um novo disco de dados gerido a um VM em Azure DevTest Labs.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório desejado. 
1. Da lista das **minhas máquinas virtuais,** selecione um VM em execução.
1. A partir do menu à esquerda, **selecione Discos**.
1. Escolha **Escolher Anexar novo** para criar um novo disco de dados e anexá-lo ao VM.

    ![Anexar novo disco de dados a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Preencha o **painel de disco novo anexar** introduzindo um nome, tipo e tamanho de disco de dados.

    ![Preencha o formulário "anexar novo disco"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecione **OK**.

Após alguns momentos, o novo disco de dados é criado e anexado ao VM e aparece na lista de discos de **dados** para esse VM.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
Siga estes passos para recolocar um disco de dados disponível existente num VM em execução. 

1. Selecione um VM em execução para o qual pretende voltar a colocar um disco de dados.
1. A partir do menu à esquerda, **selecione Discos**.
1. **Selecione Anexar a existência para** anexar um disco de dados disponível ao VM.

    ![Screenshot que mostra a definição de "Discos" selecionada e "Anexar existente" selecionada.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. A partir do painel de disco existente do **Attach,** selecione OK.

    ![Anexar o disco de dados existente a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Após alguns momentos, o disco de dados é anexado ao VM e aparece na lista de discos de **dados** para esse VM.

## <a name="detach-a-data-disk"></a>Desanexar um disco de dados
Quando já não precisa de um disco de dados ligado a um VM, pode desmontá-lo facilmente. A desprendimento remove o disco do VM, mas mantém-no armazenado para utilização posterior.

Se quiser voltar a utilizar os dados existentes no disco, pode ligá-lo novamente à mesma máquina virtual ou a outra.

### <a name="detach-from-the-vms-management-pane"></a>Retire-se do painel de gestão do VM
1. Na sua lista de máquinas virtuais, selecione um VM que tenha um disco de dados anexado.
1. A partir do menu à esquerda, **selecione Discos**.
1. A partir da lista de **discos de dados,** selecione o disco de dados que pretende destacar.

    ![Selecione discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Selecione **Desprender-se** da parte superior do painel de detalhes do disco.

    ![A screenshot mostra o painel de detalhes de um disco com a ação "Detach" realçada.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecione **Sim** para confirmar que pretende separar o disco de dados.

O disco está desligado e está disponível para ser ligado a outro VM. 
### <a name="detach-from-the-labs-main-pane"></a>Retire-se do painel principal do laboratório
1. No painel principal do seu laboratório, selecione **Os meus discos de dados.**
1. Clique com o botão direito no disco de dados que pretende desprender – ou selecione a sua elipse **(...**) – e escolha **Detach**.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecione **Sim** para confirmar que deseja desconectá-lo.

   > [!NOTE]
   > Se um disco de dados já estiver desligado, pode optar por removê-lo da sua lista de discos de dados disponíveis selecionando **Delete**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Atualizar um disco de dados não gerido
Se tiver um VM existente que utilize discos de dados não geridos, pode facilmente converter o VM para utilizar discos geridos. Este processo converte tanto o disco de SO como quaisquer discos de dados anexados.

Para atualizar um disco de dados não gerido, siga os passos descritos neste artigo para separar o disco de [dados](#detach-a-data-disk) de um VM não gerido. Em seguida, [recolocar o disco](#attach-an-existing-disk) a um VM gerido para atualizar automaticamente o disco de dados de não gerido para gerido.

## <a name="next-steps"></a>Passos seguintes
Saiba como gerir discos de dados para [máquinas virtuais reclamantes.](devtest-lab-add-claimable-vm.md#unclaim-a-vm)
