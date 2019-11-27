---
title: Tutorial – conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure
description: Neste tutorial, saiba como conceder a um usuário acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função) no portal do Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: f4dd3995df2a068824c4aa6bccca5606d250a165
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419660"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Tutorial: conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Neste tutorial, você concede a um usuário acesso para criar e gerenciar máquinas virtuais em um grupo de recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso para um usuário em um escopo de grupo de recursos
> * Remover o acesso

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

1. Na lista de navegação, clique em **grupos de recursos**.

1. Clique em **Adicionar** para abrir a folha **grupo de recursos** .

   ![Adicionar um novo grupo de recursos](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Para **nome do grupo de recursos**, digite **RBAC-Resource-Group**.

1. Selecione uma subscrição e uma localização.

1. Clique em **criar** para criar o grupo de recursos.

1. Clique em **Atualizar** para atualizar a lista de grupos de recursos.

   O novo grupo de recursos é apresentado na lista de grupos de recursos.

   ![Lista de grupos de recursos](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função.

1. Na lista de **grupos de recursos**, clique no grupo de recursos novo **RBAC-Resource-Group** .

1. Clique em **Controlo de acesso (IAM)** .

1. Clique na guia **atribuições de função** para ver a lista atual de atribuições de função.

   ![Painel Controlo de acesso (IAM) para grupo de recursos](./media/quickstart-assign-role-user-portal/access-control.png)

1. Clique em **adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desabilitada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione **Contribuidor de Máquina Virtual**.

1. Na lista **Selecionar**, selecione-se a si mesmo ou outro utilizador.

1. Clique em **salvar** para criar a atribuição de função.

   Após alguns instantes, o usuário recebe a função de colaborador da máquina virtual no escopo do grupo de recursos RBAC-Resource-Group.

   ![Atribuição da função Contribuidor de Máquina Virtual](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Remover o acesso

No RBAC, para remover o acesso, remova uma atribuição de função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário com a função colaborador da máquina virtual.

1. Clique em **remover**.

   ![Mensagem Remover atribuição de função](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Na mensagem remover atribuição de função exibida, clique em **Sim**.

## <a name="clean-up"></a>Limpeza

1. Na lista de navegação, clique em **grupos de recursos**.

1. Clique em **RBAC – grupo de recursos** para abrir o grupo de recursos.

1. Clique em **excluir grupo de recursos** para excluir o grupo de recursos.

   ![Eliminar grupo de recursos](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na folha **tem certeza de que deseja excluir** , digite o nome do grupo de recursos: **RBAC-Resource-Group**.

1. Clique em **excluir** para excluir o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: conceder a um usuário acesso aos recursos do Azure usando RBAC e Azure PowerShell](tutorial-role-assignments-user-powershell.md)
