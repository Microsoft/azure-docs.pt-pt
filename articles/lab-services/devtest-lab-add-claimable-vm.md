---
title: Criar e gerenciar VMs declaráveis no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar uma máquina virtual claimável a um laboratório no Azure DevTest Labs
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383960"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerenciar VMs declaráveis no Azure DevTest Labs
Você adiciona uma VM declarável a um laboratório de maneira semelhante a como você [adiciona uma VM padrão](devtest-lab-add-vm.md) – de uma *base* que é uma [imagem personalizada](devtest-lab-create-template.md), uma [fórmula](devtest-lab-manage-formulas.md)ou uma [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial orienta você pelo uso do portal do Azure para adicionar uma VM declarável a um laboratório no DevTest Labs e mostra os processos que um usuário segue para reivindicar e cancelar a declaração da VM.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM declarável a um laboratório no Azure DevTest Labs
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
3. Selecione **criar** para adicionar a VM especificada ao laboratório.

   A página do laboratório exibe o status da criação da VM-primeiro como **criando**e, em seguida, como **em execução** depois que a VM for iniciada.

> [!NOTE]
> Se você implantar VMs de laboratório por meio de [modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md), poderá criar VMs declaráveis definindo a propriedade **allowClaim** como true na seção Properties.


## <a name="using-a-claimable-vm"></a>Usando uma VM declarável

Um usuário pode reivindicar qualquer VM na lista de "máquinas virtuais declaráveis" seguindo uma destas etapas:

* Na lista de "máquinas virtuais declaráveis" na parte inferior do painel "visão geral" do laboratório, clique com o botão direito do mouse em uma das VMs na lista e escolha **declarar máquina**.

  ![Solicite uma VM claimável específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Na parte superior do painel "visão geral", escolha **solicitar qualquer**. Uma máquina virtual aleatória é atribuída da lista de VMs declaráveis.

  ![Solicite qualquer VM declarável.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois que um usuário alegar uma VM, o DevTest Labs iniciará o computador e o moverá para a lista de "minhas máquinas virtuais" do usuário do laboratório. Isso significa que o usuário do laboratório agora terá privilégios de proprietário neste computador. O tempo necessário para esta etapa pode variar dependendo dos horários de inicialização, bem como quaisquer outras ações personalizadas sendo executadas durante o evento de declaração. Depois de reivindicado, o computador não está mais disponível no pool de declaração.  

## <a name="unclaim-a-vm"></a>Cancelar declaração de uma VM

Quando um usuário termina de usar uma VM reivindicada e deseja disponibilizá-la para outra pessoa, ela pode retornar a VM reivindicada à lista de máquinas virtuais declaráveis executando uma destas etapas:

- Na lista de "minhas máquinas virtuais", clique com o botão direito do mouse em uma das VMs na lista – ou selecione suas reticências (...) – e escolha cancelar **declaração**.

  ![Cancele a declaração de uma VM na lista de VMs.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista de "minhas máquinas virtuais", selecione uma VM para abrir seu painel de gerenciamento e, em seguida, selecione Cancelar **declaração** na barra de menus superior.

  ![Cancele a declaração de uma VM no painel de gerenciamento da VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um usuário não alega uma VM, ele não tem mais permissões de proprietário para essa VM de laboratório específica e está disponível para ser reivindicado por qualquer outro usuário de laboratório no estado em que foi retured ao pool. 

### <a name="transferring-the-data-disk"></a>Transferindo o disco de dados
Se uma VM declarável tiver um disco de dados anexado a ele e um usuário o cancelar, o disco de dados permanecerá com a VM. Quando outro usuário declara essa VM, esse novo usuário declara o disco de dados, bem como a VM.

Isso é conhecido como "transferindo o disco de dados". Em seguida, o disco de dados fica disponível na lista do novo usuário de **meus discos de dados** para que eles sejam gerenciados.

![Cancelar a declaração de discos de dados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passos seguintes
* Após a criação, você pode se conectar à VM selecionando **conectar** no painel de gerenciamento.
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
