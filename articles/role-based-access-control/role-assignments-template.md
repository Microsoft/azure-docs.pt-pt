---
title: Adicione atribuições de funções Azure usando modelos de Gestor de Recursos Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos do Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando modelos de Gestor de Recursos Azure e controlo de acesso baseado em funções azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: baf309a93f8ba976cb6511c05ba5032ad07a0fc9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83874044"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Adicione atribuições de funções Azure usando modelos de Gestor de Recursos Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Além de utilizar o Azure PowerShell ou o Azure CLI, pode atribuir funções utilizando [modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md). Os modelos podem ser úteis se precisar de implantar recursos de forma consistente e repetida. Este artigo descreve como atribuir funções usando modelos.

## <a name="get-object-ids"></a>Obter iDs de objeto

Para atribuir uma função, precisa especificar a identificação do utilizador, grupo ou aplicação a que pretende atribuir a função. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID utilizando o portal Azure, Azure PowerShell ou Azure CLI.

### <a name="user"></a>Utilizador

Para obter a identificação de um utilizador, pode utilizar os comandos de show de utilizadores de [anúncios Get-AzADUser](/powershell/module/az.resources/get-azaduser) ou [az.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Grupo

Para obter a identificação de um grupo, pode utilizar os comandos de show de grupo [saz Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) ou [az.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Aplicação

Para obter a identificação de um diretor de serviço (identidade utilizada por uma aplicação), pode utilizar os comandos da lista [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) ou [az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Para um diretor de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso, adiciona-se uma atribuição de funções.

### <a name="resource-group-without-parameters"></a>Grupo de recursos (sem parâmetros)

O modelo seguinte mostra uma forma básica de adicionar uma atribuição de funções. Alguns valores são especificados dentro do modelo. O seguinte modelo demonstra:

-  Como atribuir a função [de Leitor](built-in-roles.md#reader) a um utilizador, grupo ou aplicação num âmbito de grupo de recursos

Para utilizar o modelo, deve fazer o seguinte:

- Crie um novo ficheiro JSON e copie o modelo
- `<your-principal-id>`Substitua-a pela identificação de um utilizador, grupo ou aplicação para atribuir a função a

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

Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e a implementação do [grupo Az criar](/cli/azure/group/deployment#az-group-deployment-create) comandos para como iniciar a implementação num grupo de recursos chamado ExemploGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição da função reader a um utilizador para um grupo de recursos após a implementação do modelo.

![Atribuição de funções no âmbito do grupo de recursos](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Grupo de recursos ou subscrição

O modelo anterior não é muito flexível. O modelo seguinte utiliza parâmetros e pode ser usado em diferentes âmbitos. O seguinte modelo demonstra:

- Como atribuir uma função a um utilizador, grupo ou aplicação em um grupo de recursos ou âmbito de subscrição
- Como especificar as funções de Proprietário, Colaborador e Leitor como parâmetro

Para utilizar o modelo, deve especificar as seguintes inputs:

- A identificação de um utilizador, grupo ou aplicação para atribuir a função a
- Um ID único que será usado para a atribuição de funções, ou você pode usar o ID padrão

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

> [!NOTE]
> Este modelo não é idempotente a menos que o mesmo `roleNameGuid` valor seja fornecido como parâmetro para cada implantação do modelo. Se não `roleNameGuid` for fornecido, por padrão, um novo GUID é gerado em cada implementação e as implementações subsequentes falharão com um `Conflict: RoleAssignmentExists` erro.

O âmbito da atribuição de funções é determinado a partir do nível da implantação. Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e a implementação do [grupo Az criar](/cli/azure/group/deployment#az-group-deployment-create) comandos para como iniciar a implementação num âmbito de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Aqui estão os exemplos [new-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [a implementação az criar](/cli/azure/deployment#az-deployment-create) comandos para como iniciar a implementação num âmbito de subscrição e especificar a localização.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Recurso

Se precisar de adicionar uma atribuição de funções ao nível de um recurso, o formato da atribuição de funções é diferente. Fornece o espaço de nome do fornecedor de recursos e o tipo de recurso do recurso para atribuir a função. Também inclui o nome do recurso em nome da atribuição de funções.

Para o tipo e nome da atribuição de funções, utilize o seguinte formato:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

O seguinte modelo demonstra:

- Como criar uma nova conta de armazenamento
- Como atribuir uma função a um utilizador, grupo ou aplicação no âmbito da conta de armazenamento
- Como especificar as funções de Proprietário, Colaborador e Leitor como parâmetro

Para utilizar o modelo, deve especificar as seguintes inputs:

- A identificação de um utilizador, grupo ou aplicação para atribuir a função a

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
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
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

Para implementar o modelo anterior, utiliza os comandos do grupo de recursos. Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e a implementação do [grupo Az criar](/cli/azure/group/deployment#az-group-deployment-create) comandos para como iniciar a implementação num âmbito de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

O seguinte mostra um exemplo da atribuição da função Do Colaborador a um utilizador para uma conta de armazenamento após a implementação do modelo.

![Atribuição de funções no âmbito dos recursos](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Novo diretor de serviço

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se criar uma nova identidade gerida e tentar atribuir uma função a esse diretor de serviço no mesmo modelo de Gestor de Recursos Azure, a atribuição de funções pode falhar. A razão para esta falha é provavelmente um atraso de replicação. O diretor de serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicao o diretor de serviço. Para abordar este cenário, deverá definir a `principalType` propriedade para quando criar a atribuição de `ServicePrincipal` funções.

O seguinte modelo demonstra:

- Como criar um novo diretor de serviço de identidade gerido
- Como especificar o`principalType`
- Como atribuir o papel de Contribuinte a esse principal de serviço num âmbito de grupo de recursos

Para utilizar o modelo, deve especificar as seguintes inputs:

- O nome base da identidade gerida, ou pode usar a cadeia predefinida

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

Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e a implementação do [grupo Az criar](/cli/azure/group/deployment#az-group-deployment-create) comandos para como iniciar a implementação num âmbito de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição de funções do Colaborador a um novo diretor de serviço de identidade gerido após a implementação do modelo.

![Atribuição de funções para um novo diretor de serviço de identidade gerido](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso a um recurso Azure, remove-se a atribuição de funções. Não há uma maneira de remover uma atribuição de funções usando um modelo. Para remover uma atribuição de funções, deve utilizar outras ferramentas, tais como:

- [Portal do Azure](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [CLI do Azure](role-assignments-cli.md#remove-a-role-assignment)
- [API REST](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Passos seguintes

- [Início Rápido: Criar e implementar um modelo do Azure Resource Manager com o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)
- [Criar grupos e recursos de recursos ao nível da subscrição](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Modelos azure quickstart](https://azure.microsoft.com/resources/templates/?term=rbac)
