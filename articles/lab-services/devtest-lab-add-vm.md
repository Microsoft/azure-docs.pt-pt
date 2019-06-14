---
title: Adicionar uma VM a um laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar uma máquina virtual a um laboratório no Azure DevTest Labs
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
ms.openlocfilehash: be5ff2c59878cc966e73d89c18343b0a6ea3d89c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311629"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM a um laboratório no Azure DevTest Labs
Se já tiver [criado a sua primeira VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), provavelmente fez isso de um pré-carregados [imagem do marketplace](devtest-lab-configure-marketplace-images.md). Agora, se pretender adicionar VMs subsequentes para seu laboratório, pode também escolher uma *base* ou seja, um um [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula](devtest-lab-manage-formulas.md). Este tutorial explica-lhe utilizar o portal do Azure para adicionar uma VM a um laboratório no DevTest Labs.

Este artigo também mostra como gerir os artefactos para uma VM no seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** no **DEVOPS** secção. Se selecionar * (em estrelas) junto a **DevTest Labs** no **DEVOPS** secção. Esta ação adiciona **DevTest Labs** para o menu de navegação à esquerda, para que pode acessá-lo facilmente na próxima vez. Em seguida, pode selecionar **DevTest Labs** no menu de navegação à esquerda.

    ![Todos os serviços - selecione o DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Na lista de laboratórios, selecione o laboratório em que pretende criar a VM.
2. O laboratório **descrição geral** página, selecione **+ adicionar**.

    ![Adicionar botão VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Sobre o **Vyberte bázi.** , selecione uma imagem do marketplace para a VM.
1. Sobre o **definições básicas** separador da **Máquina Virtual** página, efetue as seguintes ações:
    1. Introduza um nome para a VM na **nome da Máquina Virtual** caixa de texto. A caixa de texto é previamente preenchida por si com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário dentro do seu endereço de e-mail, seguido por uma série de 3 dígitos exclusiva. Esse recurso poupa tempo para pensar num nome de máquina e escrevê-lo sempre que criar uma máquina. Se desejar, pode substituir este campo preenchido automaticamente com um nome à sua escolha. Para substituir o nome preenchido automaticamente para a VM, introduza um nome na **nome da Máquina Virtual** caixa de texto.
    2. Introduza um **nome de utilizador** que é concedido privilégios de administrador na máquina virtual. O **nome de utilizador** para a máquina é previamente preenchida com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário dentro do seu endereço de e-mail. Esse recurso poupa o tempo para decidir qual a um nome de utilizador sempre que criar uma nova máquina. Novamente, pode substituir este campo preenchido automaticamente com um nome de utilizador da sua preferência, se desejar. Para substituir o valor preenchido automaticamente para o nome de utilizador, introduza um valor na **nome de utilizador** caixa de texto. Este utilizador é concedido **administrador** privilégios na máquina virtual.
    3. Se estiver a criar primeira VM no laboratório, introduza um **palavra-passe** para o utilizador. Para guardar esta palavra-passe como uma palavra-passe predefinida no Cofre de chaves do Azure associada com o laboratório, selecione **guardar como palavra-passe predefinida**. A palavra-passe predefinida é guardado no Cofre de chaves com o nome: **VmPassword**. Quando tenta criar VMs subsequentes no laboratório, **VmPassword** é selecionado automaticamente para o **palavra-passe**. Para substituir o valor, desmarque a **utilizar um segredo guardado** caixa de verificação e introduza uma palavra-passe.

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Pode também guardar segredos no Cofre de chaves em primeiro lugar e, em seguida, utilizá-lo ao criar uma VM no laboratório. Para obter mais informações, consulte [Store segredos num cofre de chaves](devtest-lab-store-secrets-in-key-vault.md). Para utilizar a palavra-passe armazenada no Cofre de chaves, selecione **utilizar um segredo guardado**e especifique um valor de chave que corresponda ao seu segredo (palavra-passe).
    4. Na **mais opções** secção, selecione **alterar tamanho**. Selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho de RAM e o tamanho de disco rígido da VM para criar.
    5. Selecione **adicionar ou remover artefactos**. Selecione e configure os artefatos que pretende adicionar à imagem de base.
    **Nota:** Se estiver familiarizado com o DevTest Labs ou configurar artefactos, consulte a [adicionar um artefato existente a uma VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) secção e, em seguida, volte aqui quando terminar.
2. Mude para o **definições avançadas** separador na parte superior, e fazer as seguintes ações:
    1. Para alterar a rede virtual que a VM está no, selecione **VNet alterar**.
    2. Para alterar a sub-rede, selecione **alterar a sub-rede**.
    3. Especifique se o endereço IP da VM **pública, privada ou partilhado**.
    4. Para eliminar automaticamente a VM, especifique a **data de expiração e hora**.
    5. Para fazer com que a VM reclamáveis por um utilizador de laboratório, selecione **Sim** para **conferir a esta máquina reclamáveis** opção.
    6. Especifique o número do **instâncias de VM** que pretende disponibilizar aos seus utilizadores de laboratório.

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecione **criar** para adicionar a VM especificada para o laboratório.

   A página de laboratório apresenta o estado da criação da VM - primeiro como **Creating**, em seguida, como **em execução** depois da VM foi iniciada.

    ![Estado de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefato existente a uma VM
Ao criar uma VM, pode adicionar artefactos existentes. Cada um deles inclui artefatos do repositório de artefactos de laboratórios DevTest público, bem como artefatos que criou e adicionado ao seu próprio repositório de artefactos.

* O Azure DevTest Labs *artefactos* permitem-lhe especificar *ações* que são executadas quando a VM está aprovisionada, como executar scripts do Windows PowerShell, executar os comandos de Bash e instalação de software.
* Artefactos *parâmetros* permitem-lhe personalizar o artefacto para o seu cenário

Para saber como criar artefactos, consulte o artigo [Aprenda a criar seu próprio artefactos para utilização com o DevTest Labs](devtest-lab-artifact-author.md).

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que contém a VM com a qual pretende trabalhar.
1. Selecione **minhas máquinas virtuais**.
1. Selecione a VM pretendida.
1. Selecione **gerir artefactos**.
1. Selecione **aplicam-se de artefactos**.
1. Sobre o **aplicam-se de artefactos** painel, selecione o artefacto que pretende adicionar à VM.
1. Sobre o **adicionar artefacto** painel, introduza os valores dos parâmetros necessários e quaisquer parâmetros opcionais que precisa.
1. Selecione **Add** para adicionar o artefacto e voltar para o **aplicam-se de artefactos** painel.
1. Continue a adicionar os artefactos conforme necessário para a sua VM.
1. Depois de adicionar os artefactos, pode [alterar a ordem na qual são executados os artefactos](#change-the-order-in-which-artifacts-are-run). Também pode aceder novamente à [ver ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando tiver terminado a adição de artefatos, selecione **aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem na qual são executados os artefactos
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionados à VM.
Os passos seguintes mostram como alterar a ordem na qual são executados os artefactos.

1. Na parte superior a **aplicam-se de artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.

    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sobre o **selecionado artefactos** painel, arraste e largue os artefactos para a ordem pretendida. **Nota:** Se tiver problemas ao arrastar o artefacto, certifique-se de que está sendo arrastado do lado esquerdo do artefacto.
1. Selecione **OK** quando terminar.

## <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os passos seguintes mostram como ver ou modificar os parâmetros de um artefato:

1. Na parte superior a **aplicam-se de artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.

    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sobre o **selecionado artefactos** painel, selecione o artefacto que pretende visualizar ou editar.
1. Sobre o **adicionar artefacto** painel, faça qualquer necessárias alterações e selecione **OK** para fechar o **adicionar artefacto** painel.
1. Selecione **OK** para fechar a **selecionado artefactos** painel.

## <a name="save-azure-resource-manager-template"></a>Guardar o modelo Azure Resource Manager
Um modelo Azure Resource Manager fornece uma forma declarativa de definir uma implementação repetível.
Os passos seguintes explicam como pode guardar o modelo Azure Resource Manager para a VM que está sendo criada.
Depois de guardar, pode utilizar o modelo Azure Resource Manager para [implementar novas VMs com o Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Sobre o **Máquina Virtual** painel, selecione **ver modelo do Azure Resource Manager**.
2. Sobre o **modelo de exibição do Azure Resource Manager** painel, selecione o texto de modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar a **painel de visualização do Azure Resource Manager modelo**.
5. Abra um editor de texto.
6. Cole o texto do modelo da área de transferência.
7. Guarde o ficheiro para utilização posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
* Quando a VM tiver sido criada, pode ligar à VM selecionando **Connect** no painel da VM.
* Saiba como [criar artefactos personalizados para a sua VM de laboratórios DevTest](devtest-lab-artifact-author.md).
* Explore os [Galeria de modelos de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
