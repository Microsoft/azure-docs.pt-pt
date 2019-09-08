---
title: Criar grupo de recursos e recursos no modelo de Azure Resource Manager de assinatura
description: Descreve como criar um grupo de recursos em um modelo de Azure Resource Manager. Ele também mostra como implantar recursos no escopo de assinatura do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772944"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos de recursos e recursos no nível da assinatura

Normalmente, você implanta recursos do Azure em um grupo de recursos em sua assinatura do Azure. No entanto, você também pode criar grupos de recursos do Azure e criar recursos do Azure no nível da assinatura. Para implantar modelos no nível de assinatura, você usa CLI do Azure e Azure PowerShell. O portal do Azure não dá suporte à implantação no nível de assinatura.

Para criar um grupo de recursos em um modelo de Azure Resource Manager, defina um recurso [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) com um nome e um local para o grupo de recursos. Você pode criar um grupo de recursos e implantar recursos nesse grupo de recursos no mesmo modelo. Os recursos que você pode implantar no nível de assinatura incluem: [Políticas](../governance/policy/overview.md)e [controle de acesso baseado em função](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Considerações sobre implementação

A implantação no nível da assinatura é diferente da implantação do grupo de recursos nos seguintes aspectos:

### <a name="schema-and-commands"></a>Esquema e comandos

O esquema e os comandos usados para implantações em nível de assinatura são diferentes das implantações de grupo de recursos. 

Para o esquema, use `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Para o comando de implantação CLI do Azure, use [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Por exemplo, o comando da CLI a seguir implanta um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Para o comando de implantação do PowerShell, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Por exemplo, o comando do PowerShell a seguir implanta um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Nome e local da implantação

Ao implantar em sua assinatura, você deve fornecer um local para a implantação. Você também pode fornecer um nome para a implantação. Se você não especificar um nome para a implantação, o nome do modelo será usado como o nome da implantação. Por exemplo, implantar um modelo chamado **azuredeploy. JSON** cria um nome de implantação padrão de **azuredeploy**.

O local das implantações de nível de assinatura é imutável. Não é possível criar uma implantação em um local quando há uma implantação existente com o mesmo nome, mas com um local diferente. Se você receber o código `InvalidDeploymentLocation`de erro, use um nome diferente ou o mesmo local da implantação anterior para esse nome.

### <a name="use-template-functions"></a>Usar funções de modelo

Para implantações em nível de assinatura, há algumas considerações importantes ao usar funções de modelo:

* **Não** há suporte para a função [resourcegroup ()](resource-group-template-functions-resource.md#resourcegroup) .
* Há suporte para a função [ResourceId ()](resource-group-template-functions-resource.md#resourceid) . Use-o para obter a ID de recurso para recursos que são usados em implantações de nível de assinatura. Por exemplo, obtenha a ID de recurso para uma definição de política com`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Há suporte para as funções [Reference ()](resource-group-template-functions-resource.md#reference) e [list ()](resource-group-template-functions-resource.md#list) .

## <a name="create-resource-groups"></a>Criar grupos de recursos

O modelo a seguir cria um grupo de recursos vazio.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

O esquema de modelo pode ser encontrado [aqui](/azure/templates/microsoft.resources/allversions). Modelos semelhantes podem ser encontrados no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Criar vários grupos de recursos

Use o [elemento copiar](resource-group-create-multiple.md) com grupos de recursos para criar mais de um grupo de recursos. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Para obter informações sobre a iteração de recursos, consulte [implantar mais de uma instância de um recurso ou propriedade em modelos de Azure Resource Manager](./resource-group-create-multiple.md)e [tutorial: Crie várias instâncias de recursos com modelos](./resource-manager-tutorial-create-multiple-instances.md)do Resource Manager.

## <a name="create-resource-group-and-deploy-resources"></a>Criar grupo de recursos e implantar recursos

Para criar o grupo de recursos e implantar recursos nele, use um modelo aninhado. O modelo aninhado define os recursos a serem implantados no grupo de recursos. Defina o modelo aninhado como dependente do grupo de recursos para certificar-se de que o grupo de recursos existe antes de implantar os recursos.

O exemplo a seguir cria um grupo de recursos e implanta uma conta de armazenamento no grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Criar políticas

### <a name="assign-policy"></a>Atribuir política

O exemplo a seguir atribui uma definição de política existente à assinatura. Se a política usar parâmetros, forneça-os como um objeto. Se a política não usar parâmetros, use o objeto vazio padrão.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Para implantar esse modelo com CLI do Azure, use:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implantar esse modelo com o PowerShell, use:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definir e atribuir política

Você pode [definir](../governance/policy/concepts/definition-structure.md) e atribuir uma política no mesmo modelo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Para criar a definição de política em sua assinatura e aplicá-la à assinatura, use o seguinte comando da CLI:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Para implantar esse modelo com o PowerShell, use:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como atribuir funções, consulte [gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de espaço de trabalho para a central de segurança do Azure, consulte [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte Criando [modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis em um modelo, consulte [funções de modelo](resource-group-template-functions.md).
