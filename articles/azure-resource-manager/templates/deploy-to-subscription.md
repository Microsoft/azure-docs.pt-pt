---
title: Mobilizar recursos para a subscrição
description: Descreve como criar um grupo de recursos num modelo de Gestor de Recursos Azure. Também mostra como implantar recursos no âmbito de subscrição do Azure.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aca1aaf9d7d0c8a97bf2dad437953ccadc02a924
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002791"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos de recursos e recursos ao nível de subscrição

Para simplificar a gestão de recursos, pode utilizar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar recursos ao nível da sua subscrição Azure. Por exemplo, pode implementar [políticas](../../governance/policy/overview.md) e [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) na sua subscrição, que as aplica através da sua subscrição. Também pode criar grupos de recursos dentro da subscrição e implementar recursos para grupos de recursos na subscrição.

> [!NOTE]
> Pode implementar 800 grupos de recursos diferentes numa implementação de nível de subscrição.

Para implementar modelos ao nível de subscrição, utilize Azure CLI, PowerShell, REST API ou o portal.

## <a name="supported-resources"></a>Recursos suportados

Nem todos os tipos de recursos podem ser implantados ao nível de subscrição. Esta secção lista quais os tipos de recursos suportados.

Para plantas Azure, utilize:

* [artefactos](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [plantas](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAsignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versões (Plantas)](/azure/templates/microsoft.blueprint/blueprints/versions)

Para políticas de Azure, utilize:

* [políticasAssinsagens](/azure/templates/microsoft.authorization/policyassignments)
* [políticasDefinições](/azure/templates/microsoft.authorization/policydefinitions)
* [políticasSetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediações](/azure/templates/microsoft.policyinsights/remediations)

Para o controlo de acesso baseado em funções, utilize:

* [papéAs de assinaturas](/azure/templates/microsoft.authorization/roleassignments)
* [funçõesDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para modelos aninhados que se implementam para grupos de recursos, utilize:

* [implementações](/azure/templates/microsoft.resources/deployments)

Para criar novos grupos de recursos, utilize:

* [grupos de recursos](/azure/templates/microsoft.resources/resourcegroups)

Para gerir a sua subscrição, utilize:

* [orçamentos](/azure/templates/microsoft.consumption/budgets)
* [apoiarPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tags](/azure/templates/microsoft.resources/tags)

Outros tipos suportados incluem:

* [âmbito As assinaturas](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventoSubscrições](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

### <a name="schema"></a>Esquema

O esquema que utiliza para implementações de nível de subscrição é diferente do esquema para implementações de grupos de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

O esquema para um ficheiro de parâmetro é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implementações de nível de subscrição são diferentes dos comandos para implementações de grupos de recursos.

Para o Azure CLI, utilize [o sub-imposição Az](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Para o comando de implantação PowerShell, utilize [o New-AzDeployment](/powershell/module/az.resources/new-azdeployment) ou **o New-AzSubscriptionDeployment**. O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Para REST API, utilize [implementações - Crie no âmbito de subscrição](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de subscrição, deve fornecer uma localização para a implementação. A localização da implantação é separada da localização dos recursos que implementa. A localização da implantação especifica onde armazenar dados de implantação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome predefinido é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo denominado **azuredeploy.jscria** um nome de implementação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não é possível criar uma implantação num local quando há uma implantação existente com o mesmo nome num local diferente. Se obter o código de erro `InvalidDeploymentLocation` , utilize um nome diferente ou o mesmo local que a colocação anterior para esse nome.

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao implementar uma subscrição, pode direcionar a subscrição ou quaisquer grupos de recursos dentro da subscrição. O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Os recursos definidos na secção de recursos do modelo são aplicados à subscrição.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        subscription-level-resources
    ],
    "outputs": {}
}
```

Para direcionar um grupo de recursos dentro da subscrição, adicione uma implementação aninhada e inclua a `resourceGroup` propriedade. No exemplo seguinte, a implantação aninhada visa um grupo de recursos chamado `rg2` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "resourceGroup": "rg2",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="use-template-functions"></a>Use funções de modelo

Para implementações de nível de subscrição, existem algumas considerações importantes ao utilizar funções de modelo:

* A função [grupo de recursos()](template-functions-resource.md#resourcegroup) **não** é suportada.
* As funções [de referência](template-functions-resource.md#reference) e [lista são](template-functions-resource.md#list) suportadas.
* Utilize a função [subscriçãoResourceId()](template-functions-resource.md#subscriptionresourceid) para obter o ID de recursos que são implantados ao nível da subscrição.

  Por exemplo, para obter o ID de recurso para uma definição de política, use:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  O ID de recurso devolvido tem o seguinte formato:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>Grupos de recursos

### <a name="create-resource-groups"></a>Criar grupos de recursos

Para criar um grupo de recursos num modelo ARM, defina um recurso [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) com um nome e localização para o grupo de recursos.

O modelo a seguir cria um grupo de recursos vazio.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Utilize o [elemento de cópia](copy-resources.md) com grupos de recursos para criar mais de um grupo de recursos.

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
      "apiVersion": "2020-06-01",
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

Para obter informações sobre a iteração de recursos, consulte [Implementar mais de um exemplo de um recurso nos Modelos do Gestor de Recursos Azure](./copy-resources.md)e [Tutorial: Criar múltiplas instâncias de recursos com modelos de Gestor de Recursos](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Criar grupo de recursos e recursos

Para criar o grupo de recursos e implementar recursos para ele, utilize um modelo aninhado. O modelo aninhado define os recursos para implantar no grupo de recursos. Desloque o modelo aninhado como dependente do grupo de recursos para se certificar de que o grupo de recursos existe antes de implantar os recursos. Pode implantar até 800 grupos de recursos.

O exemplo a seguir cria um grupo de recursos e implanta uma conta de armazenamento para o grupo de recursos.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Atribuir definição de política

O exemplo a seguir atribui uma definição de política existente à subscrição. Se a definição de política tomar parâmetros, forneça-os como um objeto. Se a definição de política não tiver parâmetros, use o objeto vazio predefinido.

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

Para implementar este modelo com O Azure CLI, utilize:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Criar e atribuir definições políticas

Pode [definir](../../governance/policy/concepts/definition-structure.md) e atribuir uma definição de política no mesmo modelo.

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

Para criar a definição de política na sua subscrição e atribuí-la à subscrição, utilize o seguinte comando CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Criar definição de planta

Pode [criar](../../governance/blueprints/tutorials/create-from-sample.md) uma definição de planta a partir de um modelo.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Para criar a definição de planta na sua subscrição, utilize o seguinte comando CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Para implementar este modelo com o PowerShell, utilize:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Controlo de acesso

Para aprender sobre a atribuição de funções, consulte atribuições de [funções Add Azure utilizando modelos do Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md).

O exemplo a seguir cria um grupo de recursos, aplica-lhe um bloqueio e atribui-lhe uma função a um principal.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

* Para um exemplo de implantação de configurações de espaço de trabalho para o Centro de Segurança Azure, consulte [deployASCwithWorkspaceSettings.jsem](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Os modelos de amostra podem ser encontrados no [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)
* Também pode implementar modelos a [nível de grupo](deploy-to-management-group.md) de gestão e [ao nível do inquilino.](deploy-to-tenant.md)
