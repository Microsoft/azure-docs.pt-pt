---
title: Lista de atribuições de funções utilizando o Azure RBAC e o portal Azure
description: Saiba como determinar que recursos utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso a usar o controlo de acesso baseado em funções azure (RBAC) e o portal Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062246"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lista de atribuições de funções utilizando o Azure RBAC e o portal Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de papéis usando o portal Azure.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utiliza a [gestão de recursos delegados do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lista de atribuições de funções para um utilizador ou grupo

A forma mais fácil de ver as funções atribuídas a um utilizador ou grupo numa subscrição é utilizar o painel de **recursos Azure.**

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione **Utilizadores** ou **Grupos**.

1. Clique no utilizador ou grupo que deseja listar as atribuições de funções para.

1. Clique em **recursos Azure**.

    Você vê uma lista de funções atribuídas ao utilizador ou grupo selecionados em vários âmbitos tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para um utilizador](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a subscrição, clique na lista de **Subscrições.**

## <a name="list-owners-of-a-subscription"></a>Lista de proprietários de uma subscrição

Os utilizadores que tenham sido atribuídos à função [Proprietário](built-in-roles.md#owner) para uma subscrição podem gerir tudo na subscrição. Siga estes passos para listar os proprietários de uma subscrição.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **subscrições.**

1. Clique na subscrição que pretende listar os proprietários.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Percorra a secção **Proprietários** para ver todos os utilizadores que foram atribuídos à função Proprietário para esta subscrição.

   ![Controlo de acesso à subscrição - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Lista de atribuições de funções num âmbito

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador de **atribuições de Role** para ver todas as atribuições de funções neste âmbito.

   ![Controlo de acesso - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments.png)

   No separador de atribuições role, você pode ver quem tem acesso neste âmbito. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a este recurso ou herdado de uma atribuição ao âmbito dos pais.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lista de atribuições de funções para um utilizador num âmbito

Para listar o acesso a um utilizador, grupo, diretor de serviço ou identidade gerida, lista as suas atribuições de funções. Siga estes passos para listar as atribuições de funções para um único utilizador, grupo, diretor de serviço ou identidade gerida num determinado âmbito.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **de acesso Verificar.**

    ![Controlo de acesso - Verifique o separador de acesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Find,** selecione o tipo de diretor de segurança que pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar o diretório para identificar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de selecionados de acesso](./media/role-assignments-list-portal/check-access-select.png)

1. Clique no diretor de segurança para abrir o painel de **atribuições.**

    ![painel de atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas ao diretor de segurança selecionado e o âmbito. Se houver alguma missão de negação neste âmbito ou herdada para este âmbito, serão listadas.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Atribuição de funções de lista para uma identidade gerida atribuída pelo sistema

1. No portal Azure, abra uma identidade gerida atribuída pelo sistema.

1. No menu esquerdo, clique em **Identidade**.

    ![Identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. No âmbito das atribuições de **funções,** clique em **mostrar as funções Azure RBAC atribuídas a esta identidade gerida**.

    Você vê uma lista de funções atribuídas à identidade gerida atribuída pelo sistema selecionada em vários âmbitos tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Lista de atribuições de funções para uma identidade gerida atribuída pelo utilizador

1. No portal Azure, abra uma identidade gerida atribuída ao utilizador.

1. Clique em **recursos Azure**.

    Você vê uma lista de funções atribuídas à identidade gerida atribuída ao utilizador selecionada em vários âmbitos tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a subscrição, clique na lista de **Subscrições.**

## <a name="list-number-of-role-assignments"></a>Número de lista de atribuições de funções

Pode ter até **2000** atribuições em cada subscrição. Para ajudá-lo a acompanhar este limite, o separador de **atribuições de Role** inclui um gráfico que lista o número de atribuições de funções para a subscrição atual.

![Controlo de acesso - Gráfico de atribuição de funções](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver perto do número máximo e tentar adicionar mais tarefas, verá um aviso no painel de atribuição de **funções Add.** Para formas de reduzir o número de atribuições de papéis, consulte [Troubleshoot Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Controlo de acesso - Adicionar aviso de atribuição de funções](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou remover atribuições de funções utilizando o Azure RBAC e o portal Azure](role-assignments-portal.md)
- [RBAC de resolução de problemas para recursos Azure](troubleshooting.md)
