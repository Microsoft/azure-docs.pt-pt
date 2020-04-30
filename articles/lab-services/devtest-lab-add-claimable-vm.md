---
title: Criar e gerir VMs reputáveis em Laboratórios Azure DevTest [ Microsoft Docs
description: Saiba como adicionar uma máquina virtual reivindicativa a um laboratório em Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270800"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerir VMs reputáveis em Laboratórios Azure DevTest
Você adiciona um VM repreensível a um laboratório de forma semelhante à forma como [adiciona um VM padrão](devtest-lab-add-vm.md) – a partir de uma *base* que é ou uma [imagem personalizada,](devtest-lab-create-template.md) [fórmula,](devtest-lab-manage-formulas.md)ou [imagem do Mercado.](devtest-lab-configure-marketplace-images.md) Este tutorial acompanha-o através da utilização do portal Azure para adicionar um VM repreensível a um laboratório em DevTest Labs, e mostra os processos que um utilizador segue para reclamar e desreclamar o VM.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar um VM reivindicavel a um laboratório em Azure DevTest Labs
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** na secção **DEVOPS.** Se selecionar * (estrela) ao lado de **DevTest Labs** na secção **DEVOPS.** Esta ação adiciona **DevTest Labs** ao menu de navegação esquerdo para que possa acessá-lo facilmente da próxima vez. Em seguida, pode selecionar **DevTest Labs** no menu de navegação esquerdo.

    ![Todos os serviços - selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Da lista de laboratórios, selecione o laboratório no qual quer criar o VM.
2. Na página **de visão geral** do laboratório, selecione + **Adicionar**.

    ![Adicione o botão VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na página base Escolha uma página **base,** selecione uma imagem de mercado para o VM.
1. No separador **Definições Básicas** da página **da máquina Virtual,** faça as seguintes ações:
    1. Introduza um nome para o VM na caixa de texto de nome de **máquina virtual.** A caixa de texto é pré-preenchida para si com um nome único gerado automaticamente. O nome corresponde ao nome de utilizador no seu endereço de e-mail seguido de um número único de 3 dígitos. Esta funcionalidade poupa-lhe tempo para pensar num nome de máquina e digitá-lo sempre que cria uma máquina. Pode substituir este campo cheio de automóveis com um nome à sua escolha, se assim o desejar. Para anular o nome preenchido automaticamente para o VM, introduza um nome na caixa de texto de **nome** virtual.
    2. Introduza um **Nome de Utilizador** que lhe seja concedido privilégios de administrador na máquina virtual. O nome de **utilizador** da máquina é pré-preenchido com um nome único gerado automaticamente. O nome corresponde ao nome de utilizador no seu endereço de e-mail. Esta funcionalidade poupa-lhe tempo para decidir um nome de utilizador sempre que cria uma nova máquina. Mais uma vez, pode substituir este campo preenchido automaticamente com um nome de utilizador à sua escolha, se assim o desejar. Para anular o valor preenchido automaticamente pelo nome do utilizador, introduza um valor na caixa de texto nome do **utilizador.** Este utilizador tem privilégios de **administrador** na máquina virtual.
    3. Se estiver a criar o primeiro VM no laboratório, introduza uma **palavra-passe** para o utilizador. Para guardar esta palavra-passe como uma palavra-passe padrão no cofre da chave Azure associado ao laboratório, selecione **Guardar como senha padrão**. A palavra-passe predefinida é guardada no cofre da chave com o nome: **VmPassword**. Quando tenta criar VMs subsequentes no laboratório, o **VmPassword** é automaticamente selecionado para a **palavra-passe**. Para anular o valor, limpe a caixa de verificação **secreta guardada** e introduza uma senha.

        Você também pode guardar segredos no cofre chave primeiro e depois usá-lo enquanto cria um VM no laboratório. Para mais informações, consulte [os segredos da Loja num cofre chave.](devtest-lab-store-secrets-in-key-vault.md) Para utilizar a palavra-passe armazenada no cofre da chave, selecione **Utilize um segredo guardado**e especifique um valor-chave que corresponda ao seu segredo (palavra-passe).
    4. Na secção **Mais opções,** selecione **Alterar tamanho**. Selecione um dos itens pré-definidos que especificam os núcleos do processador, o tamanho RAM e o tamanho do disco rígido do VM para criar.
    5. **Selecione Adicionar ou remover artefactos**. Selecione e configure os artefactos que pretende adicionar à imagem base.
    **Nota:** Se você é novo em DevTest Labs ou configurar artefactos, consulte o Adicionar um artefacto existente para uma secção [VM,](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e depois volte aqui quando estiver terminado.
2. Mude para o separador **Definições Avançadas** na parte superior e faça as seguintes ações:
    1. Para alterar a rede virtual em que o VM se encontra, selecione **Change VNet**.
    2. Para alterar a sub-rede, selecione alterar a **sub-rede**.
    3. Especifique se o endereço IP do VM é **público, privado ou partilhado**.
    4. Para eliminar automaticamente o VM, especifique a data e a hora de **validade.**
    5. Para tornar o VM repreensível por um utilizador de laboratório, selecione **Sim** for **Make this machine claimable** option.
    6. Especifique o número de **casos de VM** que pretende disponibilizar aos utilizadores do laboratório.
3. Selecione **Criar** para adicionar o VM especificado ao laboratório.

   A página de laboratório mostra o estado da criação do VM - primeiro como **Criação**, depois como **Correr** depois do VM ter sido iniciado.

> [!NOTE]
> Se implementar VMs de laboratório através de modelos do Gestor de [Recursos Azure,](devtest-lab-create-environment-from-arm.md)pode criar VMs recedíveis, definindo a propriedade de direito supérrio verdadeira na secção de propriedades. **allowClaim**


## <a name="using-a-claimable-vm"></a>Usando um VM repreensível

Um utilizador pode reclamar qualquer VM da lista de "máquinas virtuais reiváveis" fazendo uma destas etapas:

* Da lista de "máquinas virtuais reputáveis" na parte inferior do painel "Visão Geral" do laboratório, clique à direita num dos VMs da lista e escolha a **máquina De reivindicação**.

  ![Solicite um VM reclamado específico.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* No topo do painel "Visão Geral", escolha **Claim any**. Uma máquina virtual aleatória é atribuída a partir da lista de VMs repreensíveis.

  ![Solicite qualquer VM reclamado.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois de um utilizador alegar um VM, a DevTest Labs vai ligar a máquina e movê-la para a lista de utilizadores de laboratório de "As minhas máquinas virtuais". Isto significa que o utilizador do laboratório terá agora privilégios do proprietário nesta máquina. O tempo necessário para este passo pode variar consoante os tempos de arranque, bem como quaisquer outras ações personalizadas que estão a ser realizadas durante o evento de reclamação. Uma vez reclamada, a máquina já não está disponível na piscina recável.  

## <a name="unclaim-a-vm"></a>Sem reclamar um VM

Quando um utilizador terminar a utilização de um VM reclamado e quiser disponibilizá-lo a outra pessoa, pode devolver o VM reclamado à lista de máquinas virtuais reivíveis fazendo um destes passos:

- Da lista de "Minhas máquinas virtuais", clique à direita num dos VMs da lista – ou selecione a sua elipse (...) – e escolha **Unclaim**.

  ![Não reivindicar um VM na lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista de "Minhas máquinas virtuais", selecione um VM para abrir o seu painel de gestão e, em seguida, selecione **Unclaim** a partir da barra de menu seletiva superior.

  ![Não reivindicar um VM no painel de gestão da VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um utilizador não reclama um VM, já não tem permissões do proprietário para esse VM de laboratório específico e está disponível para ser reclamado por qualquer outro utilizador de laboratório no estado de que foi reposto para a piscina. 

### <a name="transferring-the-data-disk"></a>Transferência do disco de dados
Se um VM repreensível tiver um disco de dados ligado ao mesmo e um utilizador não o reclamar, o disco de dados permanece com o VM. Quando outro utilizador alega então que o VM, esse novo utilizador reclama o disco de dados, bem como o VM.

Isto é conhecido como "transferência do disco de dados". O disco de dados fica então disponível na lista dos **meus discos** de dados para que possam ser geridos.

![Discos de dados não reclamados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passos seguintes
* Uma vez criado, pode ligar-se ao VM selecionando **O Connect** no seu painel de gestão.
* Explore a galeria de [modelos quickStart Do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
