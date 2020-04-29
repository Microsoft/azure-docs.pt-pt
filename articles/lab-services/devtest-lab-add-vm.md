---
title: Adicione um VM a um laboratório em Azure DevTest Labs [ Microsoft Docs
description: Saiba como adicionar uma máquina virtual a um laboratório em Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284255"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicione um VM a um laboratório em Azure DevTest Labs
Se já [criou o seu primeiro VM,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)provavelmente fê-lo a partir de uma imagem de [mercado](devtest-lab-configure-marketplace-images.md)pré-carregada. Agora, se quiser adicionar VMs subsequentes ao seu laboratório, também pode escolher uma *base* que seja uma [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula.](devtest-lab-manage-formulas.md) Este tutorial leva-o através do portal Azure para adicionar um VM a um laboratório em DevTest Labs.

Este artigo também mostra como gerir os artefactos para um VM no seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar um VM a um laboratório em Azure DevTest Labs
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

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-virtual-machine.png)

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

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecione **Criar** para adicionar o VM especificado ao laboratório.

   A página de laboratório mostra o estado da criação do VM - primeiro como **Criação**, depois como **Correr** depois do VM ter sido iniciado.

    ![Estado de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicione um artefacto existente a um VM
Ao criar um VM, pode adicionar artefactos existentes. Cada laboratório inclui artefactos do Repositório de Artefactos de DevTest Labs públicos, bem como artefactos que criou e acrescentou ao seu próprio Repositório de Artefactos.

* Os artefactos do Azure DevTest *Labs* permitem especificar *as ações* que são executadas quando o VM é provisionado, tais como executar scripts Windows PowerShell, executar comandos Bash e instalar software.
* *Os parâmetros* do artefacto permitem personalizar o artefacto para o seu cenário particular

Para descobrir como criar artefactos, consulte o artigo, [Saiba como autoria os seus próprios artefactos para uso com devTest Labs](devtest-lab-artifact-author.md).

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório que contém o VM com o qual quer trabalhar.
1. Selecione **As minhas máquinas virtuais.**
1. Selecione o VM desejado.
1. Selecione **Gerir artefactos**.
1. Selecione **Aplicar artefactos**.
1. No painel **de artefactos Apply,** selecione o artefacto que pretende adicionar ao VM.
1. No painel **de artefactos Adicionar,** introduza os valores de parâmetro sinuoso e os parâmetros opcionais de que necessita.
1. Selecione **Adicionar** para adicionar o artefacto e voltar ao painel de **artefactos Apply.**
1. Continue a adicionar artefactos conforme necessário para o seu VM.
1. Depois de adicionar os seus artefactos, pode [alterar a ordem em que os artefactos são executados.](#change-the-order-in-which-artifacts-are-run) Também pode voltar a [ver ou modificar um artefacto.](#view-or-modify-an-artifact)
1. Quando terminar de adicionar artefactos, selecione **Apply**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem em que os artefactos são executados
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionados ao VM.
Os seguintes passos ilustram como alterar a ordem em que os artefactos são executados.

1. Na parte superior do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.

    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos selecionados,** arraste e deixe os artefactos na ordem desejada. **Nota:** Se tiver problemas em arrastar o artefacto, certifique-se de que está a arrastar-se do lado esquerdo do artefacto.
1. Selecione **OK** quando terminar.

## <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os seguintes passos ilustram como visualizar ou modificar os parâmetros de um artefacto:

1. Na parte superior do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.

    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos Selecionados,** selecione o artefacto que pretende ver ou editar.
1. No painel **de artefactos Adicionar,** faça as alterações necessárias e selecione **OK** para fechar o painel de **artefactos Adicionar.**
1. Selecione **OK** para fechar o painel de **artefactos selecionados.**

## <a name="save-azure-resource-manager-template"></a>Salvar o modelo do Gestor de Recursos Azure
Um modelo de Gestor de Recursos Azure fornece uma forma declarativa de definir uma implementação repetível.
Os seguintes passos explicam como salvar o modelo de Gestor de Recursos Azure para a criação do VM.
Uma vez guardado, pode utilizar o modelo do Gestor de Recursos Azure para [implementar novos VMs com o Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. No painel da **máquina virtual,** selecione Ver Modelo de **Gestor de Recursos Azure**.
2. No painel de modelo seletiva do Gestor de **Recursos Do View Azure,** selecione o texto do modelo.
3. Copie o texto selecionado para a pasta.
4. Selecione **OK** para fechar o painel de modelo de gestor de **recursos Do Vis Azure**.
5. Abra um editor de texto.
6. Colar no texto do modelo da prancheta.
7. Guarde o ficheiro para posterior utilização.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Uma vez criado o VM, pode ligar-se ao VM selecionando **O Connect** no painel do VM.
* Aprenda a [criar artefactos personalizados para o seu VM DevTest Labs](devtest-lab-artifact-author.md).
* Explore a galeria de [modelos QuickStart Do Gestor de Recursos DevTest Labs Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
