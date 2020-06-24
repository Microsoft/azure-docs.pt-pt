---
title: List Azure fun assignments using the Azure portal - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso ao uso do portal Azure e do controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4315b6ded8824fb119cd9c05a760b4269bcfd12d
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791066"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Liste atribuições de funções Azure usando o portal Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de funções usando o portal Azure.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de funções para um utilizador ou grupo

A maneira mais fácil de ver as funções atribuídas a um utilizador ou grupo numa subscrição é utilizar o painel de **recursos Azure.**

1. No portal Azure, selecione **Todos os serviços** do menu do portal Azure.

1. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Utilizadores** ou **Grupos**.

1. Clique no utilizador ou grupo para o que pretende listar as atribuições de funções para.

1. Clique **nos recursos de Azure.**

    Você vê uma lista de funções atribuídas ao utilizador ou grupo selecionado em vários âmbitos, tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para um utilizador](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

## <a name="list-owners-of-a-subscription"></a>Proprietários de listas de uma subscrição

Os utilizadores que tenham sido atribuídos o papel [de Proprietário](built-in-roles.md#owner) para uma subscrição podem gerir tudo na subscrição. Siga estes passos para listar os proprietários de uma subscrição.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **Em Assinaturas**.

1. Clique na subscrição de que pretende listar os proprietários de.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Percorra a secção **Proprietários** para ver todos os utilizadores que foram designados para a função Proprietário para esta subscrição.

   ![Controlo de acesso a subscrição - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de funções num âmbito

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no **separador atribuições de funções** para ver todas as atribuições de funções neste âmbito.

   ![Controlo de acesso - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments.png)

   No separador Fun assignments, pode ver quem tem acesso neste âmbito. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a este recurso ou herdado de uma atribuição ao âmbito dos pais.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de funções para um utilizador num âmbito

Para listar o acesso a um utilizador, grupo, principal de serviço ou identidade gerida, enumera as suas atribuições de funções. Siga estes passos para listar as atribuições de funções para um único utilizador, grupo, principal de serviço ou identidade gerida num determinado âmbito.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **De acesso a Verificação.**

    ![Controlo de acesso - Verifique o separador de acesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Localizar,** selecione o tipo de princípio de segurança para o qual pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acessos](./media/role-assignments-list-portal/check-access-select.png)

1. Clique no principal de segurança para abrir o painel **de atribuições.**

    ![pane atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas ao principal de segurança selecionado e o âmbito. Se houver alguma atribuição de negação neste âmbito ou herdada a este âmbito, serão listadas.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Listar atribuições de funções para uma identidade gerida atribuída pelo sistema

1. No portal Azure, abra uma identidade gerida atribuída pelo sistema.

1. No menu esquerdo, clique em **Identidade.**

    ![Identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Em **atribuições de funções**, clique **em Mostrar as funções Azure RBAC atribuídas a esta identidade gerida**.

    Você vê uma lista de funções atribuídas à identidade gerida atribuída ao sistema selecionado em vários âmbitos tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Listar atribuições de funções para uma identidade gerida atribuída pelo utilizador

1. No portal Azure, abra uma identidade gerida atribuída ao utilizador.

1. Clique **nos recursos de Azure.**

    Você vê uma lista de funções atribuídas à identidade gerida atribuída pelo utilizador selecionado em vários âmbitos, tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

## <a name="list-number-of-role-assignments"></a>Número de atribuições de funções

Pode ter até **2000** atribuições de funções em cada subscrição. Este limite inclui atribuições de funções nos âmbitos de subscrição, grupo de recursos e recursos. Para ajudá-lo a acompanhar este limite, o separador **atribuições de Função** inclui um gráfico que lista o número de atribuições de funções para a subscrição atual.

![Controlo de acesso - Número de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver a aproximar-se do número máximo e tentar adicionar mais atribuições de funções, verá um aviso no painel de atribuição de **funções Add.** Para formas de reduzir o número de atribuições de funções, consulte [Troubleshoot Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Controlo de acesso - Adicionar aviso de atribuição de funções](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicione ou remova atribuições de funções Azure usando o portal Azure](role-assignments-portal.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
