---
title: List role assignments using Azure RBAC and the Azure portal
description: Learn how to determine what resources users, groups, service principals, or managed identities have access to using Azure role-based access control (RBAC) and the Azure portal.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6117f22b24887e913ed2f8d3a43e80335121636d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934484"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>List role assignments using Azure RBAC and the Azure portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] This article describes how to list role assignments using the Azure portal.

> [!NOTE]
> If your organization has outsourced management functions to a service provider who uses [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), role assignments authorized by that service provider won't be shown here.

## <a name="list-role-assignments-for-a-user-or-group"></a>List role assignments for a user or group

The easiest way to see the roles assigned to a user or group in a subscription is to use the **Azure resources** pane.

1. In the Azure portal, click **All services** and then select **Users** or **Groups**.

1. Click the user or group you want list the role assignments for.

1. Clique em **recursos do Azure**.

    You see a list of roles assigned to the selected user or group at various scopes such as management group, subscription, resource group, or resource. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de funções para um utilizador](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a assinatura, clique na lista **assinaturas** .

## <a name="list-owners-of-a-subscription"></a>List owners of a subscription

Users that have been assigned the [Owner](built-in-roles.md#owner) role for a subscription can manage everything in the subscription. Follow these steps to list the owners of a subscription.

1. No portal do Azure, clique em **todos os serviços** e em **assinaturas**.

1. Clique na assinatura da qual você deseja listar os proprietários.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Role até a seção **proprietários** para ver todos os usuários aos quais foi atribuída a função de proprietário para esta assinatura.

   ![Controle de acesso à assinatura – guia atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de função em um escopo

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique na guia **atribuições de função** para exibir todas as atribuições de função neste escopo.

   ![Controle de acesso – guia atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na guia atribuições de função, você pode ver quem tem acesso nesse escopo. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a esse recurso ou herdado de uma atribuição para o escopo pai.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de função para um usuário em um escopo

Para listar o acesso de um usuário, grupo, entidade de serviço ou identidade gerenciada, você lista suas atribuições de função. Siga estas etapas para listar as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico.

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique na guia **verificar acesso** .

    ![Controle de acesso – guia acesso de verificação](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Localizar** , selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar o diretório em busca de nomes de exibição, endereços de email ou identificadores de objeto.

    ![Marcar lista de seleção de acesso](./media/role-assignments-list-portal/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições** .

    ![painel atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e ao escopo. Se houver quaisquer atribuições de negação neste escopo ou herdadas para esse escopo, elas serão listadas.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo sistema

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. No menu à esquerda, clique em **identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Em **atribuições de função**, clique em **Mostrar as funções de RBAC do Azure atribuídas a essa identidade gerenciada**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo sistema selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo usuário

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. Clique em **recursos do Azure**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo usuário selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a assinatura, clique na lista **assinaturas** .

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure](role-assignments-portal.md)
- [Solucionar problemas de RBAC para recursos do Azure](troubleshooting.md)
