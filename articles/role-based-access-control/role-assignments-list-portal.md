---
title: Listar atribuições de função usando o RBAC do Azure e o portal do Azure
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso do RBAC (controle de acesso baseado em função) do Azure e o portal do Azure.
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
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720734"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Listar atribuições de função usando o RBAC do Azure e o portal do Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Este artigo descreve como listar atribuições de papéis utilizando o portal Azure.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utiliza a [gestão de recursos delegados do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de função para um usuário ou grupo

A forma mais fácil de ver as funções atribuídas a um utilizador ou grupo numa subscrição é utilizar o painel de **recursos Azure.**

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione **Utilizadores** ou **Grupos**.

1. Clique no usuário ou grupo para o qual você deseja listar as atribuições de função.

1. Clique em **recursos Azure**.

    Você vê uma lista de funções atribuídas ao usuário ou grupo selecionado em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de funções para um utilizador](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a subscrição, clique na lista de **Subscrições.**

## <a name="list-owners-of-a-subscription"></a>Listar os proprietários de uma assinatura

Os utilizadores que tenham sido atribuídos à função [Proprietário](built-in-roles.md#owner) para uma subscrição podem gerir tudo na subscrição. Siga estas etapas para listar os proprietários de uma assinatura.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **subscrições.**

1. Clique na assinatura da qual você deseja listar os proprietários.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Percorra a secção **Proprietários** para ver todos os utilizadores que foram atribuídos à função Proprietário para esta subscrição.

   ![Controle de acesso à assinatura – guia atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de função em um escopo

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador de **atribuições de Role** para ver todas as atribuições de funções neste âmbito.

   ![Controle de acesso – guia atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na guia atribuições de função, você pode ver quem tem acesso nesse escopo. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a esse recurso ou herdado de uma atribuição para o escopo pai.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de função para um usuário em um escopo

Para listar o acesso de um usuário, grupo, entidade de serviço ou identidade gerenciada, você lista suas atribuições de função. Siga estas etapas para listar as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **de acesso Verificar.**

    ![Controle de acesso – guia acesso de verificação](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Find,** selecione o tipo de diretor de segurança que pretende verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar o diretório em busca de nomes de exibição, endereços de email ou identificadores de objeto.

    ![Marcar lista de seleção de acesso](./media/role-assignments-list-portal/check-access-select.png)

1. Clique no diretor de segurança para abrir o painel de **atribuições.**

    ![painel atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e ao escopo. Se houver quaisquer atribuições de negação neste escopo ou herdadas para esse escopo, elas serão listadas.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo sistema

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. No menu esquerdo, clique em **Identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. No âmbito das atribuições de **funções,** clique em **mostrar as funções Azure RBAC atribuídas a esta identidade gerida**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo sistema selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo usuário

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. Clique em **recursos Azure**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo usuário selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a subscrição, clique na lista de **Subscrições.**

## <a name="list-number-of-role-assignments"></a>Número de lista de atribuições de funções

Pode ter até **2000** atribuições em cada subscrição. Para ajudá-lo a acompanhar estes limites, o separador de **atribuições de role** inclui um gráfico que lista o número atual de atribuições de papéis.

![Controlo de acesso - Gráfico de atribuição de funções](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver perto do número máximo e tentar adicionar mais tarefas, verá um aviso no painel de atribuição de **funções Add.** Pode reduzir o número de atribuições de funções, apagando atribuições de funções que já não são necessárias ou atribuindo funções a grupos em vez de utilizadores individuais.

![Controlo de acesso - Adicionar aviso de atribuição de funções](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar ou remover atribuições de funções utilizando o Azure RBAC e o portal Azure](role-assignments-portal.md)
- [RBAC de resolução de problemas para recursos Azure](troubleshooting.md)
