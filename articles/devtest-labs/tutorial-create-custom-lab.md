---
title: Criar um laboratório com o Azure DevTest Labs | Microsoft Docs
description: Neste tutorial, você cria um laboratório em Azure DevTest Labs usando o portal Azure. Um administrador de laboratório cria um laboratório, cria VMs no laboratório, e configura políticas.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 4b59edd4ab1204d6683cf95a02070d10b1abe061
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324285"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Tutorial: Configurar um laboratório com o Azure DevTest Labs
Neste tutorial, vai criar um laboratório com o portal do Azure. O administrador do laboratório configura um laboratório numa organização, cria VMs no laboratório e configura as políticas. Os utilizadores do laboratório (por exemplo: o programador e os técnicos de teste) reivindicam as VMs no laboratório, ligam-se a elas e utilizam-nas. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório
> * Adicionar máquinas virtuais (VM) ao laboratório
> * Adicionar um utilizador à função de Utilizador do Laboratório

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab"></a>Criar um laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu principal no lado esquerdo, selecione **Criar recurso** (na parte superior da lista), aponte para **Ferramentas de programação** e clique em **DevTest Labs**. 

    ![Menu Novo DevTest Lab](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. Na janela **Criar um DevTest Lab**, realize as seguintes ações: 
    1. Em **Nome do laboratório**, introduza um nome para o laboratório. 
    2. Em **Subscrição**, selecione a subscrição na qual pretende criar o laboratório. 
    3. Em **Grupo de recursos**: selecione **Criar novo** e introduza um nome para o grupo de recursos. 
    4. Em **Localização**, selecione a localização/regiãoonde pretende criar o laboratório. 
    5. Selecione **Criar**. 
    6. Selecione **Afixar ao dashboard**. Depois de criar o laboratório, poderá vê-lo no dashboard. 

        ![Criar uma secção de laboratório do DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. Confirme que o laboratório foi criado com sucesso, analisando as notificações. Selecione **Ir para recurso**.  

    ![Notificação](./media/tutorial-create-custom-lab/creation-notification.png)
3. Confirme que vê a página do **Laboratório DevTest** para o seu laboratório. 

    ![Página inicial para o seu laboratório](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Adicionar uma VM ao laboratório

1. Na página **DevTest Lab**, selecione **+ Adicionar** na barra de ferramentas. 

    ![Botão Adicionar](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na página **Escolher uma base**, pesquise com uma palavra-chave (por exemplo: Windows, Ubuntu) e selecione uma das imagens de base na lista. 
1. Na página **Máquina virtual**, realize as seguintes ações: 
    1. Em **Nome da máquina virtual**, introduza um nome para a máquina virtual. 
    2. Para **Nome de utilizador**, introduza um nome para o utilizador com acesso à máquina virtual. 
    3. Em **Palavra-passe**,introduza a palavra-passe do utilizador. 

        ![Screenshot que mostra a página "Criar recursos de laboratório" Definições Básicas.](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Selecione O separador **definições avançadas.**
    1. Para **Tornar esta máquina reclamável**, selecione **Sim**.
    2. Confirme que a **contagem de instâncias** está definida como **1**. Se for definida como **2**, serão criadas duas VMs com os nomes: `<base image name>00' and <base image name>01`. Por exemplo: `win10vm00` e `win10vm01`.     
    3. Selecione **Submeter**. 

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. Pode ver o estado da VM na lista de **Máquinas de virtuais reclamáveis**. A criação da máquina virtual pode demorar cerca de 25 minutos. A VM é criada num grupo de recursos do Azure separado, cujo nome começa com o nome do grupo de recursos atual no qual está o laboratório. Por exemplo, se o laboratório estiver em `labrg`, a VM poderá ser criada no grupo de recursos `labrg3988722144002`. 

        ![Estado de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Uma vez criada a VM, será apresentada na lista de **Máquinas de virtuais reclamáveis**. 

    > [!NOTE] 
    > Na página **Definições Avançadas,** pode configurar um endereço IP público, privado ou partilhado para o VM. Quando o **IP partilhado** está ativado, a Azure DevTest Labs ativa automaticamente RDP para VMs windows e SSH para VMs Linux. Se criar VMs com endereços **IP públicos,** RDP e SSH estão ativados sem alterações a partir de DevTest Labs.  

## <a name="add-a-user-to-the-lab-user-role"></a>Adicionar um utilizador à função de Utilizador do Laboratório

1. Selecione **Configuração e políticas** no menu esquerdo. 

    ![Configuração e políticas](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Selecione o controlo de **acesso (IAM)** do menu e selecione + Adicione a **atribuição de funções** na barra de ferramentas. 

    ![Adicionar atribuição de função - botão](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Na página **Adicionar permissões**, realize as seguintes ações:
    1. Em **função**, selecione **Utilizador do DevTest Labs**. 
    2. Selecione o **utilizador** que pretende adicionar. 
    3. Selecione **Guardar**.

        ![Adicionar utilizador](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Limpar os recursos
O próximo tutorial mostra como um utilizador do laboratório pode reivindicar e ligar-se a uma VM no laboratório. Se não quiser completar esse tutorial e quiser limpar os recursos criados como parte deste tutorial, siga estes passos: 

1. No portal do Azure, selecione **Grupos de recursos** no menu. 

    ![Grupos de recursos](./media/tutorial-create-custom-lab/resource-groups.png)
1. Selecione o grupo de recursos no qual criou o laboratório. 
1. Selecione **Eliminar grupo de recursos**, na barra de ferramentas. A eliminação de um grupo de recursos elimina todos os recursos no grupo, incluindo o laboratório. 

    ![Grupo de recursos de laboratório](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Repita estes passos para eliminar o grupo de recursos adicional criado por si com o nome `<your resource group name><random numbers>`. Por exemplo: `splab3988722144001`. As VMs são criadas neste grupo de recursos em vez de serem criadas no grupo de recursos no qual existe o laboratório. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um laboratório com uma VM e concedeu acesso ao laboratório a um utilizador. Para saber como aceder ao laboratório enquanto um utilizador do laboratório, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Aceder ao laboratório](tutorial-use-custom-lab.md)

