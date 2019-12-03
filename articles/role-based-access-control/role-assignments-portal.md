---
title: Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o RBAC (controle de acesso baseado em função) do Azure e o portal do Azure.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707857"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] este artigo descreve como atribuir funções usando o portal do Azure.

Se você precisar atribuir funções de administrador no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- permissões de `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Visão geral do controle de acesso (IAM)

O **iam (controle de acesso)** é a folha que você usa para atribuir funções. Ele também é conhecido como gerenciamento de identidade e acesso e aparece em vários locais na portal do Azure. Veja a seguir um exemplo da folha controle de acesso (IAM) para uma assinatura.

![Folha de controle de acesso (IAM) para uma assinatura](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a folha de controle de acesso (IAM), ele ajuda se você pode responder às três perguntas a seguir ao tentar atribuir uma função:

1. **Quem precisa de acesso?**

    Quem se refere a um usuário, grupo, entidade de serviço ou identidade gerenciada. Isso também é chamado de *entidade de segurança*.

1. **De que função eles precisam?**

    As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias [funções internas](built-in-roles.md) ou usar suas próprias funções personalizadas.

1. **Onde eles precisam de acesso?**

    Em que se refere ao conjunto de recursos ao qual o acesso se aplica. Onde pode ser um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado de *escopo*.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

Siga estas etapas para atribuir uma função em escopos diferentes.

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique na guia **atribuições de função** para exibir todas as atribuições de função neste escopo.

1. Clique em **adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desabilitada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na lista **selecionar** , selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Clique em **Guardar** para atribuir a função.

   Depois de alguns instantes, a entidade de segurança recebe a função no escopo selecionado.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para tornar um usuário um administrador de uma assinatura do Azure, atribua a ele a função de [proprietário](built-in-roles.md#owner) no escopo da assinatura. A função proprietário dá ao usuário acesso completo a todos os recursos na assinatura, incluindo a permissão para conceder acesso a outras pessoas. Estes passos são iguais a qualquer outra atribuição de função.

1. No portal do Azure, clique em **todos os serviços** e em **assinaturas**.

1. Clique na assinatura em que você deseja adicionar uma atribuição de função.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Clique em **adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desabilitada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

1. Clique em **Guardar** para atribuir a função.

   Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No RBAC, para remover o acesso, remova uma atribuição de função. Siga estas etapas para remover uma atribuição de função.

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem remover atribuição de função exibida, clique em **Sim**.

    As atribuições de funções herdadas não podem ser removidas. Se precisar de remover uma atribuição de função herdada, tem de o fazer no âmbito em que a atribuição da função foi criada. Na coluna **escopo** , ao lado de **(Herdado)** , há um link que leva você até o escopo em que essa função foi atribuída. Vá para o âmbito aí listado para remover a atribuição de função.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos seguintes

- [Listar atribuições de função usando o RBAC do Azure e o portal do Azure](role-assignments-list-portal.md)
- [Tutorial: conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
- [Solucionar problemas de RBAC para recursos do Azure](troubleshooting.md)
- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)
