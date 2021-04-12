---
title: Remove ações de funções do Azure - Azure RBAC
description: Saiba como remover o acesso aos recursos Azure para utilizadores, grupos, principais serviços ou identidades geridas utilizando o portal Azure PowerShell, Azure CLI ou REST API.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100561509"
---
# <a name="remove-azure-role-assignments"></a>Remova atribuições de funções Azure

[O controlo de acesso baseado em funções Azure (Azure RBAC)](../../articles/role-based-access-control/overview.md) é o sistema de autorização que utiliza para gerir o acesso aos recursos Azure. Para remover o acesso de um recurso Azure, remova uma atribuição de funções. Este artigo descreve como remover atribuições de funções usando o portal Azure PowerShell, Azure CLI e REST API.

## <a name="prerequisites"></a>Pré-requisitos

Para remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) ou [Proprietário](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Portal do Azure

1. Controlo **de acesso aberto (IAM)** num âmbito, como grupo de gestão, subscrição, grupo de recursos ou recursos, onde pretende remover o acesso.

1. Clique no **separador atribuições de funções** para ver todas as atribuições de funções neste âmbito.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Atribuição de funções selecionada para ser removida](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Clique em **Remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-remove/remove-role-assignment.png)

1. Na mensagem de atribuição de funções removida que aparece, clique em **Sim**.

    Se vir uma mensagem de que as atribuições de funções herdadas não podem ser removidas, está a tentar remover uma tarefa de papel no âmbito de uma criança. Deve abrir o controlo de acesso (IAM) no âmbito onde a função foi atribuída e tentar novamente. Uma forma rápida de abrir o controlo de acesso (IAM) no âmbito correto é olhar para a coluna **Scope** e clicar no link ao lado **de (Herdado)**.

   ![Remover mensagem de atribuição de funções para atribuições de funções herdadas](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

No Azure PowerShell, remove-se uma atribuição de funções utilizando [o Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

O exemplo a seguir remove a atribuição de função [de contribuinte de máquina virtual](built-in-roles.md#virtual-machine-contributor) do utilizador *\@ patlong contoso.com* no grupo de recursos de vendas de *pharma:*

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Remove o papel de [Leitor](built-in-roles.md#reader) do grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-222222222222 num âmbito de subscrição.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Remove o papel de [Leitor de Faturação](built-in-roles.md#billing-reader) do *alain \@ example.com* utilizador no âmbito do grupo de gestão.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Se receber a mensagem de erro: "As informações fornecidas não mapeiam para uma atribuição de funções", certifique-se de que também especifica os `-Scope` parâmetros ou `-ResourceGroupName` parâmetros. Para mais informações, consulte [Troubleshoot Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>CLI do Azure

Em Azure CLI, remove uma atribuição de funções utilizando [a az função delete](/cli/azure/role/assignment#az_role_assignment_delete).

O exemplo a seguir remove a atribuição de função [de contribuinte de máquina virtual](built-in-roles.md#virtual-machine-contributor) do utilizador *\@ patlong contoso.com* no grupo de recursos de vendas de *pharma:*

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Remove o papel de [Leitor](built-in-roles.md#reader) do grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-222222222222 num âmbito de subscrição.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Remove o papel de [Leitor de Faturação](built-in-roles.md#billing-reader) do *alain \@ example.com* utilizador no âmbito do grupo de gestão.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>API REST

Na API REST, remove uma atribuição de funções utilizando [atribuições de funções - Eliminar](/rest/api/authorization/roleassignments/delete).

1. Obtenha o identificador de atribuição de funções (GUID). Este identificador é devolvido quando cria a atribuição de funções pela primeira vez ou pode obtê-la listando as atribuições de funções.

1. Comece com o seguinte pedido:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* com a margem para remover a atribuição de funções.

    > [!div class="mx-tableFixed"]
    > | Âmbito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gestão |
    > | `subscriptions/{subscriptionId1}` | Subscrição |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentId}* pelo identificador GUID da atribuição de funções.

O seguinte pedido elimina a atribuição de funções especificada no âmbito da subscrição:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

O seguinte mostra um exemplo da saída:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Modelo ARM

Não existe uma maneira de remover uma tarefa de função usando um modelo de Gestor de Recursos Azure (modelo ARM). Para remover uma atribuição de funções, deve utilizar outras ferramentas como o portal Azure, Azure PowerShell, Azure CLI ou REST API.

## <a name="next-steps"></a>Passos seguintes

- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
- [List Azure fun assignments using Azure PowerShell](role-assignments-list-powershell.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
