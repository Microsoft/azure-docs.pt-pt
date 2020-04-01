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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68360272"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Aceder a um laboratório no Azure DevTest Labs
Neste tutorial, vai utilizar o laboratório criado no [Tutorial: Criar um laboratório no Azure DevTest Labs](tutorial-create-custom-lab.md).

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Reclamar uma máquina virtual (VM) no laboratório
> * Ligar à VM
> * Anular reclamação da VM

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="access-the-lab"></a>Aceder ao laboratório

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
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
    > Existem outras formas de chegar à página Máquina Virtual para o seu VM. Aqui estão alguns deles: 
    > 
    > 1. Procure todos os VMs na sua subscrição. Selecione o seu VM na lista de VMs para chegar à página **Máquina Virtual.**
    > 2. Navegue na página do **Grupo de Recursos** para o grupo de recursos. Em seguida, selecione o seu VM da lista de recursos do grupo de recursos para chegar à página **Máquina Virtual.** 
    >
    > Não utilize o botão **Connect** na barra de ferramentas na página **Máquina Virtual** a que obtém utilizando estas opções. Em vez disso, navegue para a página **Máquina Virtual** a partir da página **DevTest Labs,** como mostrado neste artigo, e, em seguida, use o botão **Connect** na barra de ferramentas.


## <a name="unclaim-the-vm"></a>Anular reclamação da VM
Depois de concluir a utilização da VM, anule a reclamação dela ao seguir estes passos: 

1. Na página da máquina virtual, selecione **Anular reclamação** na barra de ferramentas. 

    ![Anular reclamação da VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A VM é encerrada antes de anular a reclamação dela. Pode ver o estado desta operação em notificações.  
3. Navegue de volta para a página do DevTest Lab clicando no seu nome de laboratório no menu de migalhas de pão no topo. 
    
    ![Navegue de volta ao laboratório](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Confirme que vê o VM na lista de **máquinas virtuais reivíveis** na parte inferior.

    
## <a name="next-steps"></a>Passos seguintes
Este tutorial mostrou como aceder e utilizar um laboratório criado com o Azure DevTest Labs. Para obter mais informações sobre como aceder e utilizar VMs num laboratório, veja 

> [!div class="nextstepaction"]
> [Procedimento: Utilizar VMs num laboratório](devtest-lab-add-vm.md)

