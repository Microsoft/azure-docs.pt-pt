---
title: Atribuir funções Azure usando modelos de Gestor de Recursos Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, principais serviços ou identidades geridas utilizando modelos de Gestor de Recursos Azure e controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: 65b4ec369085e44cdffb0550e9eeaef0196cd35a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556028"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Atribuir funções Azure usando modelos de Gestor de Recursos Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Além de utilizar o Azure PowerShell ou o Azure CLI, pode atribuir funções utilizando [modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md). Os modelos podem ser úteis se precisar de mobilizar recursos de forma consistente e repetida. Este artigo descreve como atribuir funções usando modelos.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para atribuir uma função, precisa de especificar o ID do utilizador, grupo ou aplicação a que pretende atribuir a função. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID usando o portal Azure, Azure PowerShell ou Azure CLI.

### <a name="user"></a>User

Para obter o ID de um utilizador, pode utilizar os [comandos get-AzADUser](/powershell/module/az.resources/get-azaduser) ou [az ad user show.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Para obter o ID de um grupo, você pode usar os comandos de grupo [get-AzADGroup](/powershell/module/az.resources/get-azadgroup) ou [az ad show.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Identidades geridas

Para obter o ID de uma identidade gerida, você pode usar comandos [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) ou [az ad sp.](/cli/azure/ad/sp)

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Aplicação

Para obter o ID de um principal serviço (identidade utilizada por uma aplicação), pode utilizar os comandos [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) ou [az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Para um principiante de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Atribuir um papel Azure

No Azure RBAC, para dar acesso, atribui-se um papel.

### <a name="resource-group-scope-without-parameters"></a>Âmbito do grupo de recursos (sem parâmetros)

O modelo a seguir mostra uma forma básica de atribuir um papel. Alguns valores são especificados dentro do modelo. O modelo a seguir demonstra:

-  Como atribuir a função [Reader](built-in-roles.md#reader) a um utilizador, grupo ou aplicação num âmbito de grupo de recursos

Para utilizar o modelo, tem de fazer o seguinte:

- Crie um novo ficheiro JSON e copie o modelo
- Substitua `<your-principal-id>` pelo ID de um utilizador, grupo, identidade gerida ou aplicação para atribuir a função

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

Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [o grupo de implementação az criam](/cli/azure/deployment/group#az_deployment_group_create) comandos para como iniciar a implementação num grupo de recursos chamado ExemploGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição de funções do Leitor a um utilizador para um grupo de recursos após a implementação do modelo.

![Atribuição de funções no âmbito do grupo de recursos](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Grupo de recursos ou âmbito de subscrição

O modelo anterior não é muito flexível. O modelo a seguir utiliza parâmetros e pode ser usado em diferentes âmbitos. O modelo a seguir demonstra:

- Como atribuir uma função a um utilizador, grupo ou aplicação num grupo de recursos ou no âmbito de subscrição
- Como especificar as funções de Proprietário, Colaborador e Leitor como parâmetro

Para utilizar o modelo, deve especificar as seguintes entradas:

- O ID de um utilizador, grupo, identidade gerida ou aplicação para atribuir o papel a
- Um ID único que será usado para a atribuição de funções, ou pode usar o ID padrão

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
> Este modelo não é idempotente a menos que o mesmo `roleNameGuid` valor seja fornecido como parâmetro para cada implementação do modelo. Se não `roleNameGuid` for fornecido, por padrão é gerado um novo GUID em cada implementação e as implementações subsequentes falharão com um `Conflict: RoleAssignmentExists` erro.

O âmbito da atribuição de funções é determinado a partir do nível da implantação. Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [o grupo de implementação az criam](/cli/azure/deployment/group#az_deployment_group_create) comandos para como iniciar a implementação num âmbito de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Aqui estão os exemplos [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [az deployment sub criar](/cli/azure/deployment/sub#az_deployment_sub_create) comandos para como iniciar a implementação num âmbito de subscrição e especificar a localização.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Âmbito do recurso

Se precisar de atribuir uma função ao nível de um recurso, desaprote a `scope` propriedade na atribuição de funções para o nome do recurso.

O modelo a seguir demonstra:

- Como criar uma nova conta de armazenamento
- Como atribuir uma função a um utilizador, grupo ou aplicação no âmbito da conta de armazenamento
- Como especificar as funções de Proprietário, Colaborador e Leitor como parâmetro

Para utilizar o modelo, deve especificar as seguintes entradas:

- O ID de um utilizador, grupo, identidade gerida ou aplicação para atribuir o papel a

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
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

Para implementar o modelo anterior, utilize os comandos do grupo de recursos. Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [o grupo de implementação az criam](/cli/azure/deployment/group#az_deployment_group_create) comandos para como iniciar a implementação num âmbito de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

O seguinte mostra um exemplo da atribuição de função do Contribuinte a um utilizador para uma conta de armazenamento após a implementação do modelo.

![Atribuição de funções no âmbito de recursos](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Novo diretor de serviços

Se criar um novo diretor de serviço e tentar imediatamente atribuir um papel a esse diretor de serviço, essa atribuição de funções pode falhar em alguns casos. Por exemplo, se criar uma nova identidade gerida e tentar atribuir uma função a esse principal de serviço no mesmo modelo de Gestor de Recursos Azure, a atribuição de funções pode falhar. A razão para esta falha é provavelmente um atraso de replicação. O principal serviço é criado numa região; no entanto, a atribuição de funções pode ocorrer em uma região diferente que ainda não replicou o principal serviço.

Para abordar este cenário, deverá definir o `principalType` imóvel para `ServicePrincipal` a criação da atribuição de funções. Também deve definir a `apiVersion` atribuição de funções para `2018-09-01-preview` ou mais tarde.

O modelo a seguir demonstra:

- Como criar um novo diretor de serviço de identidade gerido
- Como especificar o `principalType`
- Como atribuir o papel de Contribuinte a esse principal de serviço num âmbito de grupo de recursos

Para utilizar o modelo, deve especificar as seguintes entradas:

- O nome base da identidade gerida, ou pode usar a cadeia padrão

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
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Aqui estão os exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [o grupo de implementação az criam](/cli/azure/deployment/group#az_deployment_group_create) comandos para como iniciar a implementação num âmbito de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição de funções do Contribuinte a um novo diretor de serviço de identidade gerido após a implementação do modelo.

![Atribuição de funções para um novo diretor de serviço de identidade gerido](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Criar e implementar modelos ARM utilizando o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of ARM templates](../azure-resource-manager/templates/template-syntax.md) (Compreender a estrutura e a sintaxe dos modelos do Resource Manager)
- [Criar grupos de recursos e recursos ao nível de subscrição](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
