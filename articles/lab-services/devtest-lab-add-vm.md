---
title: Adicionar uma VM a um laboratório no Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976500"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM a um laboratório no Azure DevTest Labs
Se você já [criou sua primeira VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), provavelmente fez isso a partir de uma [imagem do Marketplace](devtest-lab-configure-marketplace-images.md)carregada previamente. Agora, se você quiser adicionar VMs subsequentes ao laboratório, também poderá escolher uma *base* que seja uma [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula](devtest-lab-manage-formulas.md). Este tutorial orienta você pelo uso do portal do Azure para adicionar uma VM a um laboratório no DevTest Labs.

Este artigo também mostra como gerenciar os artefatos de uma VM em seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na seção **DEVOPS** . Se você selecionar * (estrela) ao lado de **DevTest Labs** na seção **DEVOPS** . Essa ação adiciona o **DevTest Labs** ao menu de navegação à esquerda para que você possa acessá-lo facilmente na próxima vez. Em seguida, você pode selecionar **DevTest Labs** no menu de navegação à esquerda.

    ![Todos os serviços-selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.
2. Na página **visão geral** do laboratório, selecione **+ Adicionar**.

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na página **escolher uma base** , selecione uma imagem do Marketplace para a VM.
1. Na guia **configurações básicas** da página **máquina virtual** , execute as seguintes ações:
    1. Insira um nome para a VM na caixa de texto **nome da máquina virtual** . A caixa de texto é preenchida previamente com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário dentro do seu endereço de email seguido por um número exclusivo de 3 dígitos. Esse recurso economiza o tempo de reflexão de um nome de computador e o digita sempre que você cria um computador. Você pode substituir esse campo de preenchimento automático com um nome de sua escolha, se desejar. Para substituir o nome de preenchimento automático da VM, insira um nome na caixa de texto **nome da máquina virtual** .
    2. Insira um **nome de usuário** que receba privilégios de administrador na máquina virtual. O **nome de usuário** para o computador é preenchido previamente com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário dentro do seu endereço de email. Esse recurso poupa o tempo para decidir sobre um nome de usuário toda vez que você cria um novo computador. Novamente, você pode substituir esse campo de preenchimento automático com um nome de usuário de sua escolha, se desejar. Para substituir o valor preenchido automaticamente para nome de usuário, insira um valor na caixa de texto **nome de usuário** . Esse usuário recebe privilégios de **administrador** na máquina virtual.
    3. Se você estiver criando a primeira VM no laboratório, insira uma **senha** para o usuário. Para salvar essa senha como uma senha padrão no cofre de chaves do Azure associada ao laboratório, selecione **salvar como senha padrão**. A senha padrão é salva no cofre de chaves com o nome: **VmPassword**. Quando você tenta criar VMs subsequentes no laboratório, o **VmPassword** é selecionado automaticamente para a **senha**. Para substituir o valor, desmarque a caixa de seleção **usar um segredo salvo** e insira uma senha.

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Você também pode salvar os segredos no cofre de chaves primeiro e, em seguida, usá-los durante a criação de uma VM no laboratório. Para obter mais informações, consulte [armazenar segredos em um cofre de chaves](devtest-lab-store-secrets-in-key-vault.md). Para usar a senha armazenada no cofre de chaves, selecione **usar um segredo salvo**e especifique um valor de chave que corresponda ao seu segredo (senha).
    4. Na seção **mais opções** , selecione **alterar tamanho**. Selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
    5. Selecione **Adicionar ou remover artefatos**. Selecione e configure os artefatos que você deseja adicionar à imagem base.
    **Observação:** Se você for novo no DevTest Labs ou configurando artefatos, consulte a seção [Adicionar um artefato existente a uma VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e, em seguida, retornar aqui quando terminar.
2. Alterne para a guia **Configurações avançadas** na parte superior e execute as seguintes ações:
    1. Para alterar a rede virtual em que a VM está, selecione **alterar VNet**.
    2. Para alterar a sub-rede, selecione **alterar sub-rede**.
    3. Especifique se o endereço IP da VM é **público, privado ou compartilhado**.
    4. Para excluir automaticamente a VM, especifique a **data e a hora de expiração**.
    5. Para tornar a VM declarável por um usuário do laboratório, selecione **Sim** para **tornar esta** opção de declaração de máquina.
    6. Especifique o número de **instâncias da VM** que você deseja disponibilizar para os usuários do laboratório.

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecione **criar** para adicionar a VM especificada ao laboratório.

   A página do laboratório exibe o status da criação da VM-primeiro como **criando**e, em seguida, como **em execução** depois que a VM for iniciada.

    ![Estado de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefato existente a uma VM
Ao criar uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do repositório público de artefatos do DevTest Labs, bem como artefatos que você criou e adicionou ao seu próprio repositório de artefatos.

* Os *artefatos* Azure DevTest Labs permitem especificar *ações* que são executadas quando a VM é provisionada, como executar scripts do Windows PowerShell, executar comandos bash e instalar software.
* Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico

Para descobrir como criar artefatos, consulte o artigo [saiba como crie seus próprios artefatos para uso com o DevTest Labs](devtest-lab-artifact-author.md).

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
1. Na lista de laboratórios, selecione o laboratório que contém a VM com a qual você deseja trabalhar.
1. Selecione **minhas máquinas virtuais**.
1. Selecione a VM desejada.
1. Selecione **gerenciar artefatos**.
1. Selecione **aplicar artefatos**.
1. No painel **aplicar artefatos** , selecione o artefato que você deseja adicionar à VM.
1. No painel **Adicionar artefato** , insira os valores de parâmetro necessários e os parâmetros opcionais necessários.
1. Selecione **Adicionar** para adicionar o artefato e retornar ao painel **aplicar artefatos** .
1. Continue adicionando artefatos conforme necessário para sua VM.
1. Depois de adicionar seus artefatos, você pode [alterar a ordem na qual os artefatos são executados](#change-the-order-in-which-artifacts-are-run). Você também pode voltar para [Exibir ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando você terminar de adicionar artefatos, selecione **aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem na qual os artefatos são executados
Por padrão, as ações dos artefatos são executadas na ordem em que são adicionadas à VM.
As etapas a seguir ilustram como alterar a ordem na qual os artefatos são executados.

1. Na parte superior do painel **aplicar artefatos** , selecione o link que indica o número de artefatos que foram adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **artefatos selecionados** , arraste e solte os artefatos na ordem desejada. **Observação:** Se você tiver problemas ao arrastar o artefato, certifique-se de que você está arrastando do lado esquerdo do artefato.
1. Selecione **OK** quando terminar.

## <a name="view-or-modify-an-artifact"></a>Exibir ou modificar um artefato
As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior do painel **aplicar artefatos** , selecione o link que indica o número de artefatos que foram adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **artefatos selecionados** , selecione o artefato que você deseja exibir ou editar.
1. No painel **Adicionar artefato** , faça as alterações necessárias e selecione **OK** para fechar o painel **Adicionar artefato** .
1. Selecione **OK** para fechar o painel **artefatos selecionados** .

## <a name="save-azure-resource-manager-template"></a>Salvar Azure Resource Manager modelo
Um modelo de Azure Resource Manager fornece uma maneira declarativa de definir uma implantação repetível.
As etapas a seguir explicam como salvar o modelo de Azure Resource Manager para a VM que está sendo criada.
Depois de salvo, você pode usar o modelo de Azure Resource Manager para [implantar novas VMs com Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. No painel **máquina virtual** , selecione **Exibir Azure Resource Manager modelo**.
2. No painel **exibir Azure Resource Manager modelo** , selecione o texto do modelo.
3. Copiar o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar o **painel exibir Azure Resource Manager modelo**.
5. Abra um editor de texto.
6. Cole o texto do modelo da área de transferência.
7. Salve o arquivo para uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Depois que a VM tiver sido criada, você poderá se conectar à VM selecionando **conectar** no painel da VM.
* Saiba como [criar artefatos personalizados para sua VM do DevTest Labs](devtest-lab-artifact-author.md).
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
