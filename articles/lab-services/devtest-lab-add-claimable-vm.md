---
title: Criar e gerir VMs reclamáveis num laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar uma máquina virtual reclamáveis a um laboratório no Azure DevTest Labs
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
ms.openlocfilehash: f83ee40c97e49add539a44a6ba764024a8e47bfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081496"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerir VMs reclamáveis no Azure DevTest Labs
Adicionar uma VM reclamável a um laboratório de forma semelhante a como [adicionar uma VM padrão](devtest-lab-add-vm.md) – num *base* ou seja, seja um [imagem personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md) , ou [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial explica-lhe utilizar o portal do Azure para adicionar uma VM reclamável a um laboratório no DevTest Labs e mostra os processos de um utilizador que se segue para reclamar e removerá os a VM.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar uma VM reclamável a um laboratório no Azure DevTest Labs
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
3. Selecione **criar** para adicionar a VM especificada para o laboratório.

   A página de laboratório apresenta o estado da criação da VM - primeiro como **Creating**, em seguida, como **em execução** depois da VM foi iniciada.

> [!NOTE]
> Se implementar VMs do laboratório através de [modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md), pode criar VMs reclamáveis definindo a **allowClaim** propriedade como true na secção de propriedades.


## <a name="using-a-claimable-vm"></a>Utilizar uma VM reclamável

Um utilizador pode reivindicar qualquer VM na lista de "Máquinas de virtuais Reclamáveis" efetuando um dos seguintes passos:

* Na lista de "Máquinas de virtuais Reclamáveis" na parte inferior do painel de "Descrição geral" do laboratório, faça duplo clique das VMs na lista e escolha **máquina de afirmação**.

  ![Pedir uma VM reclamável específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Na parte superior do painel "Descrição geral", escolha **afirmação qualquer**. Uma máquina virtual aleatória é atribuída a partir da lista de VMs reclamáveis.

  ![Qualquer VM reclamável do pedido.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois de uma VM de afirmações de um utilizador, é atrasado em sua lista de "Minhas máquinas virtuais" e não se encontra reclamáveis por qualquer outro utilizador.

## <a name="unclaim-a-vm"></a>Adicione uma VM

Quando um usuário concluído com uma VM solicitada e quiser torná-la disponível para outra pessoa, eles podem retornar a VM solicitada para a lista de máquinas de virtuais reclamáveis efetuando um dos seguintes passos:

- Na lista de "Minhas máquinas virtuais", com o botão direito em uma das VMs na lista – ou selecione nas respetivas reticências (.....) – e escolha **Unclaim**.

  ![Adicione uma VM na lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista de "Minhas máquinas virtuais", selecione uma VM para abrir o painel de gestão, em seguida, selecione **Unclaim** da barra de menus superior.

  ![Adicione uma VM no painel de gestão da VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um utilizador unclaims uma VM, já não tem quaisquer permissões para essa VM de laboratório específica.

### <a name="transferring-the-data-disk"></a>Transferir o disco de dados
Se uma VM reclamável tem um disco de dados anexado a ele e um utilizador unclaims-lo, o disco de dados mantém-se com a VM. Quando outro usuário, em seguida, as afirmações que a VM, que o utilizador novo afirmações o disco de dados, bem como a VM.

Isso é conhecido como "Transferir o disco de dados". O disco de dados, em seguida, fica disponível na lista do novo usuário **Moje datové disky** para os mesmos gerir.

![Removerá os discos de dados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passos Seguintes
* Depois de criado, pode ligar à VM selecionando **Connect** no seu painel de gestão.
* Explore os [Galeria de modelos de início rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
