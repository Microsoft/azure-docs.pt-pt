---
title: Criar o grupo de recursos e recursos na subscrição - modelo Azure Resource Manager
description: Descreve como criar um grupo de recursos num modelo Azure Resource Manager. Ela também mostra como implementar recursos no âmbito da subscrição do Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: bdba294e1ee776d90b93f715e930ec26765abb7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129160"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos de recursos e recursos ao nível da subscrição

Normalmente, implementa recursos do Azure para um grupo de recursos na sua subscrição do Azure. No entanto, também pode criar grupos de recursos do Azure e criar recursos do Azure ao nível da subscrição. Para implementar modelos ao nível da subscrição, pode utilizar o CLI do Azure e o Azure PowerShell. O portal do Azure não suporta a implementação no nível da subscrição.

Para criar um grupo de recursos num modelo Azure Resource Manager, defina uma [ **Microsoft.Resources/resourceGroups** ](/azure/templates/microsoft.resources/allversions) recurso com um nome e local para o grupo de recursos. Pode criar um grupo de recursos e implementar recursos no grupo de recursos no mesmo modelo. Os recursos que pode implementar ao nível da subscrição incluem: [As políticas](../governance/policy/overview.md), e [controlo de acesso baseado em funções](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-considerations"></a>Considerações sobre implementação

Implementação de nível de subscrição é diferente da implementação do grupo de recursos nos seguintes aspetos:

### <a name="schema-and-commands"></a>Esquema e os comandos

O esquema e os comandos que utilizar para implementações de nível de assinatura são diferentes de implementações do grupo de recursos. 

Para o esquema, utilize `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Para o comando de implementação da CLI do Azure, utilize [criar a implementação de az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Por exemplo, o seguinte comando da CLI implementa um modelo para criar um grupo de recursos:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Para o comando de implementação do PowerShell, utilize [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Por exemplo, o seguinte comando PowerShell implementa um modelo para criar um grupo de recursos:

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Nome da implementação e a localização

Ao implementar a sua subscrição, tem de fornecer uma localização para a implementação. Também pode fornecer um nome para a implementação. Se não especificar um nome para a implementação, o nome do modelo é utilizado como o nome da implementação. Por exemplo, implementar um modelo com o nome **azuredeploy. JSON** cria um nome de implementação do padrão de **azuredeploy**.

A localização das implementações de nível de subscrição é imutável. Não é possível criar uma implementação num único local quando existe uma implementação existente com o mesmo nome mas localização diferente. Se receber o código de erro `InvalidDeploymentLocation`, utilize um nome diferente ou a mesma localização como a implementação anterior para esse nome.

### <a name="use-template-functions"></a>Utilize as funções de modelo

Para implementações de nível de assinatura, existem algumas considerações importantes ao utilizar as funções de modelo:

* O [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) função é **não** suportado.
* O [resourceId()](resource-group-template-functions-resource.md#resourceid) função é suportada. Utilize-o para obter o ID de recurso para recursos que são utilizados em implementações de nível de subscrição. Por exemplo, obter o ID de recurso para uma definição de política com `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* O [reference()](resource-group-template-functions-resource.md#reference) e [List ()](resource-group-template-functions-resource.md#list) as funções são suportadas.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O modelo seguinte cria um grupo de recursos vazio.

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

O esquema de modelo pode ser encontrado em [aqui](/azure/templates/microsoft.resources/allversions). Modelos semelhantes podem ser encontrados em [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Criar vários grupos de recursos

Utilize o [elemento de cópia](resource-group-create-multiple.md) com grupos de recursos para criar mais do que um grupo de recursos. 

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

Para obter informações sobre a iteração de recursos, consulte [implementar mais de uma instância de um recurso ou uma propriedade nos modelos do Azure Resource Manager](./resource-group-create-multiple.md), e [Tutorial: Criar várias instâncias de recursos com modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Criar grupo de recursos e implementar recursos

Para criar o grupo de recursos e implementar recursos na mesma, utilize um modelo aninhado. O modelo aninhado define os recursos necessários para implementar o grupo de recursos. Defina o modelo aninhado como o grupo de recursos para se certificar de que o grupo de recursos existe antes de implementar os recursos dependentes.

O exemplo seguinte cria um grupo de recursos e implementa uma conta de armazenamento para o grupo de recursos.

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

O exemplo seguinte atribui uma definição de política existente para a subscrição. Se a política tem parâmetros, forneça-los como um objeto. Se a política não aceitam parâmetros, utilize o objeto vazio padrão.

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

Para aplicar uma política incorporada a sua subscrição do Azure, utilize os seguintes comandos do CLI do Azure:

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Para aplicar uma política incorporada a sua subscrição do Azure, utilize os seguintes comandos do CLI do Azure:

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
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

Pode [definir](../governance/policy/concepts/definition-structure.md) e atribuir uma política no modelo do mesmo.

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

Para criar a definição de política na sua subscrição e aplicá-la para a subscrição, utilize o seguinte comando da CLI:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>Criar funções

### <a name="assign-role-at-subscription"></a>Atribuir função na subscrição

O exemplo seguinte atribui uma função a um utilizador ou grupo para a subscrição. Neste exemplo, não especifique um âmbito para a atribuição de uma vez que o âmbito é automaticamente definido para a subscrição.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Para atribuir um grupo do Active Directory a uma função para a sua subscrição, utilize os seguintes comandos do CLI do Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>Atribuir função no âmbito

O modelo de nível de subscrição seguinte atribui uma função a um utilizador ou grupo que está confinado para um grupo de recursos dentro da subscrição. O âmbito tem de ser igual ou abaixo do nível de implementação. Pode implementar para uma subscrição e especificar uma atribuição de função no âmbito de um grupo de recursos nessa subscrição. No entanto, não é possível implementar um grupo de recursos e especificar um âmbito de atribuição de função à subscrição.

Para atribuir a função a um âmbito, utilize uma implementação aninhada. Tenha em atenção que o nome do grupo de recursos é especificado nas propriedades para o recurso de implantação e na propriedade do âmbito da atribuição de função.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
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

Para atribuir um grupo do Active Directory a uma função para a sua subscrição, utilize os seguintes comandos do CLI do Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo de implementação de definições de área de trabalho para o Centro de segurança do Azure, veja [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Para saber mais sobre a criação de modelos Azure Resource Manager, veja [criação de modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](resource-group-template-functions.md).
