---
title: Adicione um VM a um laboratório em Azure DevTest Labs Microsoft Docs
description: Aprenda a usar o portal Azure para adicionar uma máquina virtual a um laboratório em Azure DevTest Labs. Pode escolher uma base que seja uma imagem personalizada ou uma fórmula.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297628"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicione um VM a um laboratório em Azure DevTest Labs
Se já [criou o seu primeiro VM,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)provavelmente fê-lo a partir de uma imagem de [mercado](devtest-lab-configure-marketplace-images.md)pré-carregada. Agora, se quiser adicionar VMs subsequentes ao seu laboratório, também pode escolher uma *base* que seja uma [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula](devtest-lab-manage-formulas.md). Este tutorial acompanha-o através do portal Azure para adicionar um VM a um laboratório em DevTest Labs.

Este artigo também mostra como gerir os artefactos para um VM no seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar um VM a um laboratório em Azure DevTest Labs
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

        ![Screenshot que mostra a página "Criar recurso de laboratório" com "Definições Básicas" selecionadas e os valores de "Definições do Utilizador" introduzidos.](./media/tutorial-create-custom-lab/new-virtual-machine.png)

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

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecione **Criar** para adicionar o VM especificado ao laboratório.

   A página de laboratório exibe o estado da criação do VM - primeiro como **Criar**- e depois como **Running** after the VM foi iniciado.

    ![Estado de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicione um artefacto existente a um VM
Ao criar um VM, pode adicionar artefactos existentes. Cada laboratório inclui artefactos do Repositório de Artefactos de Laboratórios Públicos, bem como artefactos que criou e adicionou ao seu próprio Repositório de Artefactos.

* Os artefactos da Azure DevTest Labs permitem especificar *as ações* que são *executadas* quando o VM é a provisionado, tais como executar scripts Windows PowerShell, executar comandos Bash e instalar software.
* Os *parâmetros do* artefacto permitem personalizar o artefacto para o seu cenário particular

Para descobrir como criar artefactos, veja o artigo, [Aprenda a autorar os seus próprios artefactos para uso com a DevTest Labs](devtest-lab-artifact-author.md).

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que contém o VM com o qual pretende trabalhar.
1. Selecione **As minhas máquinas virtuais.**
1. Selecione o VM desejado.
1. **Selecione Gerir artefactos**.
1. **Selecione Aplicar artefactos**.
1. No painel de **artefactos Apply,** selecione o artefacto que pretende adicionar ao VM.
1. No painel **de artefactos Add,** introduza os valores de parâmetros necessários e quaisquer parâmetros opcionais de que necessita.
1. **Selecione Adicione** para adicionar o artefacto e volte ao painel de **artefactos Aplicar.**
1. Continue a adicionar artefactos conforme necessário para o seu VM.
1. Depois de adicionar os seus artefactos, pode [alterar a ordem em que os artefactos são executados.](#change-the-order-in-which-artifacts-are-run) Também pode voltar a [ver ou modificar um artefacto.](#view-or-modify-an-artifact)
1. Quando terminar de adicionar artefactos, **selecione Aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem em que os artefactos são executados
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionadas ao VM.
Os seguintes passos ilustram como alterar a ordem em que os artefactos são executados.

1. No topo do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.

    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na vidraça de **artefactos selecionados,** arraste e deixe cair os artefactos na ordem desejada. **Nota:** Se tiver problemas em arrastar o artefacto, certifique-se de que está a arrastar-se pelo lado esquerdo do artefacto.
1. Selecione **OK** quando terminar.

## <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os seguintes passos ilustram como ver ou modificar os parâmetros de um artefacto:

1. No topo do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.

    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos selecionados,** selecione o artefacto que pretende visualizar ou editar.
1. No painel **de artefactos Add,** faça as alterações necessárias e selecione **OK** para fechar o painel de **artefactos Add.**
1. Selecione **OK** para fechar o painel **de artefactos selecionados.**

## <a name="save-azure-resource-manager-template"></a>Salvar modelo de gestor de recursos Azure
Um modelo de Gestor de Recursos Azure fornece uma forma declarativa de definir uma implementação repetível.
Os passos seguintes explicam como guardar o modelo do Gestor de Recursos Azure para a criação do VM.
Uma vez guardado, pode utilizar o modelo Azure Resource Manager para [implementar novos VMs com Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. No **painel de máquinas virtual,** selecione **Ver Modelo do Gestor de Recursos Azure**.
2. No painel de **modelo do View Azure Resource Manager,** selecione o texto do modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar o **painel de modelo do Gestor de Recursos View Azure**.
5. Abra um editor de texto.
6. Cole no texto do modelo a partir da prancheta.
7. Guarde o ficheiro para utilização posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Uma vez criado o VM, pode ligar-se ao VM selecionando **Connect** no painel de VM.
* Saiba como [criar artefactos personalizados para o seu VM DevTest Labs](devtest-lab-artifact-author.md).
* Explore a [galeria de modelos QuickStart do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
