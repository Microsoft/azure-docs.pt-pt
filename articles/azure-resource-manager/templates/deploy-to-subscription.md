---
title: Implementar recursos para a subscrição
description: Descreve como criar um grupo de recursos num modelo de Gestor de Recursos Azure. Mostra também como utilizar recursos no âmbito de subscrição do Azure.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 50db0b4d46ff4e367411829aa75fa017a168372f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207660"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos e recursos de recursos ao nível da subscrição

Normalmente, você implementa recursos Azure para um grupo de recursos na sua subscrição Azure. No entanto, também pode criar recursos ao nível da subscrição. Utiliza-se implementações de nível de subscrição para tomar medidas que façam sentido a esse nível, tais como a criação de grupos de recursos ou a atribuição [de controlo de acesso baseado em papéis.](../../role-based-access-control/overview.md)

Para implementar modelos ao nível da subscrição, utilize o Azure CLI, PowerShell ou REST API. O portal Azure não suporta a implantação no nível de subscrição.

## <a name="supported-resources"></a>Recursos suportados

Pode implementar os seguintes tipos de recursos ao nível da subscrição:

* [implementações](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [políticasAtribuis](/azure/templates/microsoft.authorization/policyassignments)
* [definições políticas](/azure/templates/microsoft.authorization/policydefinitions)
* [definições políticasSetDefinições](/azure/templates/microsoft.authorization/policysetdefinitions)
* [recursosGrupos](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definições de papel](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema que utiliza para implementações de nível de subscrição é diferente do esquema para implementações de grupos de recursos.

Para os modelos, utilize:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implementações de nível de subscrição são diferentes dos comandos para implementações de grupos de recursos.

Para o Azure CLI, utilize a criação de [implementação az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). O exemplo seguinte implementa um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


Para o comando de implementação PowerShell, utilize [new-AzDeployment](/powershell/module/az.resources/new-azdeployment). O exemplo seguinte implementa um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Para rest API, utilize [implementações - Criar no âmbito de subscrição](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de subscrição, deve fornecer um local para a implementação. A localização da implantação é separada da localização dos recursos que implementa. O local de implementação especifica onde armazenar dados de implementação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome padrão é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não se pode criar uma implantação num local quando existe uma implantação existente com o mesmo nome num local diferente. Se obtê-lo com o código de erro `InvalidDeploymentLocation`, utilize um nome diferente ou o mesmo local que a implementação anterior para esse nome.

## <a name="use-template-functions"></a>Funções de modelo de utilização

Para implementações de nível de subscrição, existem algumas considerações importantes ao utilizar funções de modelo:

* A função de [recurso Group()](template-functions-resource.md#resourcegroup) **não** é suportada.
* As funções de [referência](template-functions-resource.md#reference) e [lista](template-functions-resource.md#list) são suportadas.
* A função [resourceId()](template-functions-resource.md#resourceid) é suportada. Use-o para obter o ID de recursos que são usados em implementações de nível de subscrição. Não forneça um valor para o parâmetro do grupo de recursos.

  Por exemplo, para obter o ID de recurso para uma definição de política, use:
  
  ```json
  resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  O ID de recurso devolvido tem o seguinte formato:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

  Ou, utilize a função [de subscriçãoResourceId()](template-functions-resource.md#subscriptionresourceid) para obter o ID de recurso para um recurso de nível de subscrição.

## <a name="create-resource-groups"></a>Criar grupos de recursos

Para criar um grupo de recursos num modelo de Gestor de Recursos Azure, defina um recurso [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) com um nome e localização para o grupo de recursos. Você pode criar um grupo de recursos e implementar recursos para esse grupo de recursos no mesmo modelo.

O seguinte modelo cria um grupo de recursos vazios.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Utilize o [elemento de cópia](copy-resources.md) com grupos de recursos para criar mais do que um grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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

Para obter informações sobre a iteração de recursos, consulte [Implementar mais do que uma instância de um recurso nos modelos](./copy-resources.md)do Gestor de Recursos Azure , e [Tutorial: Criar múltiplas instâncias](./template-tutorial-create-multiple-instances.md)de recursos com modelos de Gestor de Recursos .

## <a name="resource-group-and-resources"></a>Grupo de recursos e recursos

Para criar o grupo de recursos e implementar recursos para ele, use um modelo aninhado. O modelo aninhado define os recursos para implantar para o grupo de recursos. Desloque o modelo aninhado como dependente do grupo de recursos para garantir que o grupo de recursos existe antes de implantar os recursos.

O exemplo seguinte cria um grupo de recursos e implementa uma conta de armazenamento para o grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
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

### <a name="assign-policy"></a>Política de atribuição

O exemplo seguinte atribui uma definição de política existente à subscrição. Se a política tiver parâmetros, forneça-os como um objeto. Se a apólice não tomar parâmetros, use o objeto vazio por defeito.

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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Para implantar este modelo com o Azure CLI, utilize:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implementar este modelo com o PowerShell, utilize:

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

Pode [definir](../../governance/policy/concepts/definition-structure.md) e atribuir uma apólice no mesmo modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
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

Para criar a definição de política na sua subscrição e aplicá-la à subscrição, utilize o seguinte comando CLI:

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

## <a name="template-samples"></a>Exemplos de modelo

* Crie um grupo de recursos, bloqueie-o e dê-lhe permissões. Veja [aqui.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)
* Criar um grupo de recursos, uma política e uma atribuição de políticas.  Veja [aqui.](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a atribuição de funções, consulte [Gerir o acesso aos recursos do Azure utilizando modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para um exemplo de implementação de definições de espaço de trabalho para o Centro de Segurança Azure, consulte [o deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Os modelos de amostra podem ser encontrados no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Para aprender sobre a criação de modelos de Gestor de Recursos Azure, consulte [os modelos de autor.](template-syntax.md)
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](template-functions.md).
