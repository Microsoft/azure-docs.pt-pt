---
title: Compreender o âmbito para o Azure RBAC
description: Saiba mais sobre o âmbito do controlo de acesso baseado em funções Azure (Azure RBAC) e como determinar a margem para um recurso.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856160"
---
# <a name="understand-scope-for-azure-rbac"></a>Compreender o âmbito para o Azure RBAC

*Âmbito* é o conjunto de recursos a que o acesso se aplica. Quando atribuis um papel, é importante compreender o alcance para que possas conceder a um diretor de segurança o acesso que realmente precisa. Limitando o âmbito, limita-se os recursos em risco se o diretor de segurança estiver comprometido.

## <a name="scope-levels"></a>Níveis de âmbito

Em Azure, pode especificar um âmbito a quatro níveis: [grupo de gestão,](../governance/management-groups/overview.md)subscrição, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups)e recursos. Os âmbitos são estruturados numa relação de principal-subordinado. Cada nível de hierarquia torna o âmbito mais específico. Pode atribuir funções em qualquer um destes níveis de âmbito. O nível selecionado determina a largura da função. Níveis mais baixos herdam permissões de função de níveis mais altos. 

![Âmbito de uma atribuição de função](./media/scope-overview/rbac-scope-no-label.png)

Os grupos de gestão são um nível de âmbito acima das subscrições, mas os grupos de gestão apoiam hierarquias mais complexas. O diagrama que se segue mostra um exemplo de uma hierarquia de grupos de gestão e subscrições que pode definir. Para obter mais informações sobre grupos de gestão, veja [o que são os grupos de gestão Azure?](../governance/management-groups/overview.md)

![Grupo de gestão e hierarquia de subscrição](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Formato de âmbito

Se adicionar atribuições de funções utilizando a linha de comando, terá de especificar o âmbito. Para ferramentas de linha de comando, o âmbito é uma cadeia potencialmente longa que identifica o âmbito exato da atribuição de funções. No portal Azure, este âmbito é tipicamente listado como o *ID de recurso*.

O âmbito consiste numa série de identificadores separados pelo carácter do corte (/). Pode pensar nesta cadeia como expressando a seguinte hierarquia, onde o texto sem espaços reservados `{}` () são identificadores fixos:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` é o ID da subscrição a utilizar (um GUID).
- `{resourcesGroupName}` é o nome do grupo de recursos contendo.
- `{providerName}` é o nome do [fornecedor](../azure-resource-manager/management/azure-services-resource-providers.md) de recursos que trata do recurso, em seguida, `{resourceType}` e identificar `{resourceSubType*}` outros níveis dentro desse fornecedor de recursos.
- `{resourceName}` é a última parte da cadeia que identifica um recurso específico.

Os grupos de gestão são um nível superior às subscrições e têm o âmbito mais amplo (menos específico). As atribuições de funções a este nível aplicam-se a subscrições dentro do grupo de gestão. A margem para um grupo de gestão tem o seguinte formato:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Exemplos de âmbito

> [!div class="mx-tableFixed"]
> | Âmbito | Exemplo |
> | --- | --- |
> | Grupo de gestão | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscrição | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Grupo de recursos | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Recurso | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Como determinar a margem de manobra para um recurso

É bastante simples determinar a margem para um grupo de gestão, subscrição ou grupo de recursos. Só precisa saber o nome e a identificação da assinatura. No entanto, determinar a margem de manobra para um recurso requer um pouco mais de trabalho. Aqui estão algumas maneiras de determinar a possibilidade de um recurso.

- No portal Azure, abra o recurso e, em seguida, olhe para as propriedades. O recurso deve listar o **ID de recurso** onde pode determinar o âmbito. Por exemplo, aqui estão os IDs de recursos para uma conta de armazenamento.

    ![IDs de recursos para uma conta de armazenamento no portal Azure](./media/scope-overview/scope-resource-id.png)

- Outra forma é usar o portal Azure para atribuir uma função temporariamente no âmbito do recurso e, em seguida, usar [Azure PowerShell](role-assignments-list-powershell.md) ou [Azure CLI](role-assignments-list-cli.md) para listar a atribuição de funções. Na saída, o âmbito será listado como um imóvel.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Passos seguintes

- [Passos para adicionar uma atribuição de função](role-assignments-steps.md)
- [Resource providers for Azure services](../azure-resource-manager/management/azure-services-resource-providers.md) (Fornecedor de recursos para serviços do Azure)
- [O que são os grupos de gestão do Azure?](../governance/management-groups/overview.md)
