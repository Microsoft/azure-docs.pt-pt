---
title: Criar e gerir VMs reclamais em Azure DevTest Labs Microsoft Docs
description: Aprenda a usar o portal Azure para adicionar uma máquina virtual reivindicada em Azure DevTest Labs e veja os processos a seguir para reivindicar/não reclamar uma máquina virtual.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270984"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerir VMs reclamais em Azure DevTest Labs
Adiciona-se um VM reclamado a um laboratório de forma semelhante à forma como [adiciona um VM padrão](devtest-lab-add-vm.md) – a partir de uma *base* que é ou uma [imagem personalizada,](devtest-lab-create-template.md) [fórmula](devtest-lab-manage-formulas.md)ou imagem [do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial acompanha-o através do portal Azure para adicionar um VM reclamado a um laboratório em DevTest Labs, e mostra os processos que um utilizador segue para reivindicar e desclamar o VM.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar um VM reclamado a um laboratório em Azure DevTest Labs
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** na secção **DEVOPS.** Se selecionar * (estrela) ao lado **de DevTest Labs** na secção **DEVOPS.** Esta ação adiciona **a DevTest Labs** ao menu de navegação à esquerda para que possa aceder facilmente ao mesmo da próxima vez. Em seguida, pode selecionar **DevTest Labs** no menu de navegação à esquerda.

    ![Todos os serviços - selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Na lista de laboratórios, selecione o laboratório no qual pretende criar o VM.
2. Na **página** geral do laboratório, selecione **+ Adicionar**.

    ![Adicionar botão VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na página base escolha uma página **base,** selecione uma imagem de mercado para o VM.
1. No separador **Definições Básicas** da página **da máquina Virtual,** faça as seguintes ações:
    1. Introduza um nome para o VM na caixa de texto de nome da **máquina Virtual.** A caixa de texto é pré-preenchida para si com um nome auto-gerado único. O nome corresponde ao nome de utilizador dentro do seu endereço de e-mail seguido de um número único de 3 dígitos. Esta funcionalidade poupa-lhe tempo para pensar num nome de máquina e digitá-lo sempre que cria uma máquina. Pode anular este campo preenchido automaticamente com um nome à sua escolha, se assim o desejar. Para anular o nome preenchido automaticamente para o VM, introduza um nome na caixa de texto **do nome da máquina Virtual.**
    2. Introduza um **Nome de Utilizador** que seja concedido privilégios de administrador na máquina virtual. O **nome de utilizador** da máquina é pré-preenchido com um nome auto-gerado único. O nome corresponde ao nome de utilizador no seu endereço de e-mail. Esta funcionalidade poupa-lhe tempo para decidir sobre um nome de utilizador sempre que cria uma nova máquina. Mais uma vez, pode substituir este campo preenchido automaticamente com um nome de utilizador à sua escolha, se assim o desejar. Para anular o valor preenchido automaticamente para o nome de utilizador, introduza um valor na caixa de texto **do Nome do Utilizador.** Este utilizador tem privilégios **de administrador** na máquina virtual.
    3. Se estiver a criar o primeiro VM em laboratório, introduza uma **palavra-passe** para o utilizador. Para guardar esta palavra-passe como uma palavra-passe padrão no cofre da chave Azure associado ao laboratório, **selecione Guardar como senha padrão**. A palavra-passe predefinida é guardada no cofre da chave com o nome: **VmPassword**. Quando tenta criar VMs subsequentes em laboratório, o **VmPassword** é automaticamente selecionado para a **palavra-passe**. Para anular o valor, limpe a **caixa de verificação secreta guardada** e introduza uma palavra-passe.

        Você também pode guardar segredos no cofre da chave primeiro e depois usá-lo enquanto cria um VM no laboratório. Para mais informações, consulte [os segredos da Loja num cofre de chaves.](devtest-lab-store-secrets-in-key-vault.md) Para utilizar a palavra-passe armazenada no cofre da chave, selecione **Use um segredo guardado**e especifique um valor chave que corresponda ao seu segredo (palavra-passe).
    4. Na secção **Mais opções,** selecione **Alterar o tamanho**. Selecione um dos itens predefinidos que especificam os núcleos do processador, o tamanho do RAM e o tamanho do disco rígido do VM para criar.
    5. Selecione **Adicionar ou Remover Artefactos**. Selecione e configure os artefactos que pretende adicionar à imagem base.
    **Nota:** Se você é novo em DevTest Labs ou configurar artefactos, consulte o Adicionar um artefacto existente a uma secção [VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e, em seguida, volte aqui quando terminar.
2. Mude para o separador **Definições Avançadas** na parte superior e faça as seguintes ações:
    1. Para alterar a rede virtual em que o VM está, selecione **Change VNet**.
    2. Para alterar a sub-rede, selecione **Alterar a sub-rede**.
    3. Especificar se o endereço IP do VM é **público, privado ou partilhado**.
    4. Para eliminar automaticamente o VM, especifique a **data e a hora de validade**.
    5. Para tornar o VM reclamativo por um utilizador de laboratório, selecione **Sim** para Tornar esta máquina a opção **reivindicativa.**
    6. Especifique o número **de casos de VM** que pretende disponibilizá-lo aos utilizadores do seu laboratório.
3. Selecione **Criar** para adicionar o VM especificado ao laboratório.

   A página de laboratório exibe o estado da criação do VM - primeiro como **Criar**- e depois como **Running** after the VM foi iniciado.

> [!NOTE]
> Se implementar VMs de laboratório através [dos modelos Azure Resource Manager,](devtest-lab-create-environment-from-arm.md)pode criar VMs reclamados definindo a propriedade **allowClaim** para ser verdadeira na secção de propriedades.


## <a name="using-a-claimable-vm"></a>Usando um VM reclamavel

Um utilizador pode reclamar qualquer VM da lista de "máquinas virtuais reclamantes" fazendo um destes passos:

* Da lista de "Máquinas virtuais reclamantes" na parte inferior do painel "Overview" do laboratório, clique à direita num dos VMs da lista e escolha a **máquina Claim**.

  ![Solicite um VM específico reclamante.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* No topo do painel "Visão Geral", escolha **reclamar qualquer**. Uma máquina virtual aleatória é atribuída a partir da lista de VMs reclamados.

  ![Solicite qualquer VM reclamante.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois de um utilizador alegar um VM, a DevTest Labs irá ligar a máquina e movê-la para a lista de utilizadores do laboratório de "As minhas máquinas virtuais". Isto significa que o utilizador do laboratório terá agora privilégios de proprietário nesta máquina. O tempo necessário para este passo pode variar dependendo dos tempos de arranque, bem como de quaisquer outras ações personalizadas que estão a ser realizadas durante o evento de reclamação. Uma vez reclamada, a máquina já não está disponível na piscina reclamada.  

## <a name="unclaim-a-vm"></a>Unclaim a VM

Quando um utilizador termina usando um VM reclamado e quer disponibilizá-lo a outra pessoa, pode devolver o VM reclamado à lista de máquinas virtuais reclamadas, fazendo um destes passos:

- Na lista de "As minhas máquinas virtuais", clique à direita num dos VMs da lista – ou selecione a sua elipse (...) – e escolha **Unclaim**.

  ![Desclamar um VM na lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista de "As minhas máquinas virtuais", selecione um VM para abrir o painel de gestão e, em seguida, selecione **Unclaim** a partir da barra de menu superior.

  ![Desclamar um VM no painel de gestão do VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um utilizador não alega um VM, já não tem permissões do proprietário para esse VM de laboratório específico e está disponível para ser reclamado por qualquer outro utilizador de laboratório no estado em que foi devolvido à piscina. 

### <a name="transferring-the-data-disk"></a>Transferência do disco de dados
Se um VM reclamado tiver um disco de dados ligado a ele e um utilizador o não alegar, o disco de dados permanece com o VM. Quando outro utilizador afirma então que o VM, o novo utilizador reclama o disco de dados, bem como o VM.

Isto é conhecido como "transferir o disco de dados". O disco de dados fica então disponível na lista dos meus discos de **dados** para que possam gerir.

![Desclam discos de dados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passos seguintes
* Uma vez criado, pode ligar-se ao VM selecionando **Connect** no seu painel de gestão.
* Explore a [galeria de modelos QuickStart do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
