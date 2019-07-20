---
title: Aceder a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Neste tutorial, vai aceder ao laboratório criado com o Azure DevTest Labs, reclamar máquinas virtuais, utilizá-las e, em seguida, anular a reclamação.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: ee9a68df685095244fc9471b7d4ab0f6cee0642d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360272"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Acessar um laboratório no Azure DevTest Labs
Neste tutorial, você usa o laboratório que foi criado no [tutorial: Crie um laboratório no Azure DevTest Labs](tutorial-create-custom-lab.md) .

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Reclamar uma máquina virtual (VM) no laboratório
> * Ligar à VM
> * Anular reclamação da VM

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="access-the-lab"></a>Aceder ao laboratório

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu da esquerda. 
3. Selecione **DevTest Labs** para tipo de recurso. 
4. Selecione o laboratório. 

    ![Selecionar o laboratório](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Reclamar uma VM

1. Na lista de **Máquinas de virtuais reclamáveis**, selecione **...** (reticências) e, em seguida, **Reclamar máquina**.

    ![Reclamar máquina virtual](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Confirme que vê a VM na lista **Minhas máquinas virtuais**.

    ![Minha máquina virtual](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Ligar à VM

1. Selecione a VM na lista. Verá a **página Máquina Virtual** da sua VM. Selecione **Ligar** na barra de ferramentas.

    ![Conectar à máquina virtual](./media/tutorial-use-custom-lab/connect-button.png)
2. Guarde o ficheiro **RDP** transferido no disco rígido e utilize-o para ligar à máquina virtual. Especifique o nome de utilizador e a palavra-passe mencionados quando a VM foi criada na secção anterior. 

    Para ligar a uma VM do Linux, o acesso SSH e/ou RDP tem de estar ativado para a VM. Para obter passos para ligar a uma VM do Linux através de RDP, veja [Instalar e configurar o Ambiente de Trabalho Remoto para ligar a uma VM do Linux no Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Há outras maneiras de acessar a página da máquina virtual para sua VM. Aqui estão algumas delas: 
    > 
    > 1. Pesquise todas as VMs em sua assinatura. Selecione sua VM na lista de VMs para acessar a página da **máquina virtual** .
    > 2. Navegue até a página do **grupo de recursos** do grupo de recursos. Em seguida, selecione sua VM na lista de recursos no grupo de recursos para acessar a página da **máquina virtual** . 
    >
    > Não use o botão **conectar** na barra de ferramentas na página da **máquina virtual** que você obtém usando essas opções. Em vez disso, navegue até a página da **máquina virtual** na página do **DevTest Labs** , conforme mostrado neste artigo, e use o botão **conectar** na barra de ferramentas.


## <a name="unclaim-the-vm"></a>Anular reclamação da VM
Depois de concluir a utilização da VM, anule a reclamação dela ao seguir estes passos: 

1. Na página da máquina virtual, selecione **Anular reclamação** na barra de ferramentas. 

    ![Anular reclamação da VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A VM é encerrada antes de anular a reclamação dela. Você pode ver o status dessa operação em notificações.  
3. Navegue de volta para a página do laboratório do DevTest clicando no nome do laboratório no menu de navegação estrutural na parte superior. 
    
    ![Navegue de volta para o laboratório](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Confirme que você vê a VM na lista de **máquinas virtuais claimáveis** na parte inferior.

    
## <a name="next-steps"></a>Passos Seguintes
Este tutorial mostrou como aceder e utilizar um laboratório criado com o Azure DevTest Labs. Para obter mais informações sobre como aceder e utilizar VMs num laboratório, veja 

> [!div class="nextstepaction"]
> [How to: Usar VMs em um laboratório](devtest-lab-add-vm.md)

