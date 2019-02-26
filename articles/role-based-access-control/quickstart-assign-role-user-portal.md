---
title: Tutorial - conceder acesso de um utilizador aos recursos do Azure utilizando o RBAC e o portal do Azure | Documentos da Microsoft
description: Saiba como conceder um acesso de utilizador aos recursos do Azure com o controlo de acesso baseado em funções (RBAC) no portal do Azure.
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
ms.openlocfilehash: 9352130eeeb68a87947450cb5616aa59e8e99ae9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807376"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o portal do Azure

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Neste tutorial, pode conceder um acesso de utilizador para criar e gerir máquinas virtuais num grupo de recursos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso para um utilizador com um âmbito de grupo de recursos
> * Remover o acesso

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Na lista de navegação, clique em **grupos de recursos**.

1. Clique em **Add** para abrir o **grupo de recursos** painel.

   ![Adicionar um novo grupo de recursos](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Para **nome do grupo de recursos**, introduza **grupo de recursos de rbac**.

1. Selecione uma subscrição e uma localização.

1. Clique em **criar** para criar o grupo de recursos.

1. Clique em **atualizar** para atualizar a lista de grupos de recursos.

   O novo grupo de recursos é apresentado na lista de grupos de recursos.

   ![Lista de grupos de recursos](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, crie uma atribuição de função.

1. Na lista de **grupos de recursos**, clique no novo **grupo de recursos de rbac** grupo de recursos.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **atribuições de funções** guia para ver a lista atual de atribuições de funções.

   ![Painel Controlo de acesso (IAM) para grupo de recursos](./media/quickstart-assign-role-user-portal/access-control.png)

1. Clique em **Add** > **adicionar atribuição de função** para abrir o painel de atribuição de função de adicionar.

   Se não tiver permissões para atribuir funções, a opção de atribuição de função de adicionar será desativada.

   ![Menu Adicionar](./media/role-assignments-portal/add-menu.png)

   ![Adicionar o painel de atribuição de função](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione **Contribuidor de Máquina Virtual**.

1. Na lista **Selecionar**, selecione-se a si mesmo ou outro utilizador.

1. Clique em **guardar** para criar a atribuição de função.

   Após alguns instantes, o utilizador tem atribuída a função de contribuinte de Máquina Virtual no âmbito do grupo de recursos do grupo de recursos de rbac.

   ![Atribuição da função Contribuidor de Máquina Virtual](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Remover o acesso

No RBAC, para remover o acesso, remova uma atribuição de função.

1. Na lista de atribuições de função, adicione uma marca de verificação junto ao utilizador com a função de contribuinte de Máquina Virtual.

1. Clique em **remover**.

   ![Mensagem Remover atribuição de função](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Na mensagem de atribuição de função remove que aparece, clique em **Sim**.

## <a name="clean-up"></a>Limpeza

1. Na lista de navegação, clique em **grupos de recursos**.

1. Clique em **grupo de recursos de rbac** para abrir o grupo de recursos.

1. Clique em **eliminar grupo de recursos** para eliminar o grupo de recursos.

   ![Eliminar grupo de recursos](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Sobre o **tem a certeza de que pretende eliminar** painel, escreva o nome do grupo de recursos: **grupo de recursos de rbac**.

1. Clique em **eliminar** para eliminar o grupo de recursos.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)

