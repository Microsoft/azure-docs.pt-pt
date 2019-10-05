---
title: Gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager | Microsoft Docs
description: Saiba como gerenciar o acesso aos recursos do Azure para usuários, grupos e aplicativos usando o controle de acesso baseado em função (RBAC) e modelos de Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b4eebf7dac4d388411f570b1546c96e3b82b2a98
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950067"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Além de usar Azure PowerShell ou o CLI do Azure, você pode gerenciar o acesso aos recursos do Azure usando [modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Os modelos podem ser úteis se você precisar implantar recursos de forma consistente e repetida. Este artigo descreve como você pode gerenciar o acesso usando RBAC e modelos.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Criar uma atribuição de função em um escopo de grupo de recursos (sem parâmetros)

No RBAC, para conceder acesso, crie uma atribuição de função. O modelo a seguir mostra uma maneira básica de criar uma atribuição de função. Alguns valores são especificados no modelo. O modelo a seguir demonstra:

-  Como atribuir a função [leitor](built-in-roles.md#reader) a um usuário, grupo ou aplicativo em um escopo de grupo de recursos

Para usar o modelo, você deve fazer o seguinte:

- Criar um novo arquivo JSON e copiar o modelo
- Substitua `<your-principal-id>` pelo identificador exclusivo de um usuário, grupo ou aplicativo ao qual atribuir a função. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Aqui estão exemplos de comandos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) para saber como iniciar a implantação em um grupo de recursos denominado myGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Veja a seguir um exemplo de atribuição de função de leitor para um usuário para um grupo de recursos após a implantação do modelo.

![Atribuição de função no escopo do grupo de recursos](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Criar uma atribuição de função em um grupo de recursos ou escopo de assinatura

O modelo anterior não é muito flexível. O modelo a seguir usa parâmetros e pode ser usado em escopos diferentes. O modelo a seguir demonstra:

- Como atribuir uma função a um usuário, grupo ou aplicativo em um grupo de recursos ou escopo de assinatura
- Como especificar as funções de proprietário, colaborador e leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:

- O identificador exclusivo de um usuário, grupo ou aplicativo ao qual atribuir a função
- A função a ser atribuída
- Um identificador exclusivo que será usado para a atribuição de função ou você poderá usar o identificador padrão

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Para obter o identificador exclusivo de um usuário ao qual atribuir a função, você pode usar os comandos [Get-AzADUser](/powershell/module/az.resources/get-azaduser) ou [AZ ad User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

O escopo da atribuição de função é determinado do nível da implantação. Aqui estão exemplos de comandos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) para saber como iniciar a implantação em um escopo de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Aqui estão exemplos de comandos [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) para saber como iniciar a implantação em um escopo de assinatura e especificar o local.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

> [!NOTE]
> Esse modelo não é idempotente, a menos que o mesmo valor `roleNameGuid` seja fornecido como um parâmetro para cada implantação do modelo. Se nenhum `roleNameGuid` for fornecido, por padrão, um novo GUID será gerado em cada implantação e implantações subsequentes falharão com um erro de `Conflict: RoleAssignmentExists`.

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Criar uma atribuição de função em um escopo de recurso

Se você precisar criar uma atribuição de função no nível de um recurso, o formato da atribuição de função será diferente. Você fornece o namespace do provedor de recursos e o tipo de recurso do recurso ao qual atribuir a função. Você também inclui o nome do recurso no nome da atribuição de função.

Para o tipo e o nome da atribuição de função, use o seguinte formato:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

O modelo a seguir demonstra:

- Como criar uma nova conta de armazenamento
- Como atribuir uma função a um usuário, grupo ou aplicativo no escopo da conta de armazenamento
- Como especificar as funções de proprietário, colaborador e leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:

- O identificador exclusivo de um usuário, grupo ou aplicativo ao qual atribuir a função
- A função a ser atribuída

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(parameters('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Para implantar o modelo anterior, use os comandos do grupo de recursos. Aqui estão exemplos de comandos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) para saber como iniciar a implantação em um escopo de recurso.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Veja a seguir um exemplo de atribuição de função de colaborador para um usuário para uma conta de armazenamento depois de implantar o modelo.

![Atribuição de função no escopo de recurso](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Criar uma atribuição de função para uma nova entidade de serviço

Se você criar uma nova entidade de serviço e tentar atribuir imediatamente uma função a essa entidade de serviço, essa atribuição de função poderá falhar em alguns casos. Por exemplo, se você criar uma nova identidade gerenciada e, em seguida, tentar atribuir uma função a essa entidade de serviço no mesmo modelo de Azure Resource Manager, a atribuição de função poderá falhar. O motivo dessa falha é provavelmente um atraso de replicação. A entidade de serviço é criada em uma região; no entanto, a atribuição de função pode ocorrer em uma região diferente que ainda não tenha replicado a entidade de serviço. Para resolver esse cenário, você deve definir a propriedade `principalType` como `ServicePrincipal` ao criar a atribuição de função.

O modelo a seguir demonstra:

- Como criar uma nova entidade de serviço de identidade gerenciada
- Como especificar o `principalType`
- Como atribuir a função de colaborador a essa entidade de serviço em um escopo de grupo de recursos

Para usar o modelo, você deve especificar as seguintes entradas:

- O nome de base da identidade gerenciada ou você pode usar a cadeia de caracteres padrão

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Aqui estão exemplos de comandos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) para saber como iniciar a implantação em um escopo de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Veja a seguir um exemplo da atribuição de função de colaborador para uma nova entidade de serviço de identidade gerenciada após a implantação do modelo.

![Atribuição de função para uma nova entidade de serviço de identidade gerenciada](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Criar e implantar modelos de Azure Resource Manager usando o portal do Azure @ no__t-0
- [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)
- [Criar grupos de recursos e recursos no nível da assinatura](../azure-resource-manager/deploy-to-subscription.md)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
