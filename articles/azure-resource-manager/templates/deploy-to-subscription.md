---
title: Mobilizar recursos para a subscrição
description: Descreve como criar um grupo de recursos num modelo de Gestor de Recursos Azure. Também mostra como implantar recursos no âmbito de subscrição do Azure.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ba055970a8fac71b798fca9d3c76550cb7d482a7
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178998"
---
# <a name="subscription-deployments-with-arm-templates"></a>Implementações de subscrição com modelos ARM

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

Para o controlo de acesso baseado em funções Azure (Azure RBAC), utilize:

* [papéAs de assinaturas](/azure/templates/microsoft.authorization/roleassignments)
* [funçõesDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para modelos aninhados que se implementam para grupos de recursos, utilize:

* [implementações](/azure/templates/microsoft.resources/deployments)

Para criar novos grupos de recursos, utilize:

* [grupos de recursos](/azure/templates/microsoft.resources/resourcegroups)

Para gerir a sua subscrição, utilize:

* [Configurações de conselheiro](/azure/templates/microsoft.advisor/configurations)
* [orçamentos](/azure/templates/microsoft.consumption/budgets)
* [Alterar perfil de análise](/azure/templates/microsoft.changeanalysis/profile)
* [apoiarPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tags](/azure/templates/microsoft.resources/tags)

Outros tipos suportados incluem:

* [âmbito As assinaturas](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventoSubscrições](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Esquema

O esquema que utiliza para implementações de nível de subscrição é diferente do esquema para implementações de grupos de recursos.

Para modelos, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    ...
}
```

O esquema para um ficheiro de parâmetro é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandos de implantação

Para implementar uma subscrição, utilize os comandos de implementação de nível de subscrição.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para o Azure CLI, utilize [o sub-imposição Az](/cli/azure/deployment/sub#az-deployment-sub-create). O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o comando de implantação PowerShell, utilize [o New-AzDeployment](/powershell/module/az.resources/new-azdeployment) ou **o New-AzSubscriptionDeployment**. O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Para obter informações mais detalhadas sobre comandos de implantação e opções para a implementação de modelos ARM, consulte:

* [Implementar recursos com modelos ARM e portal Azure](deploy-portal.md)
* [Implementar recursos com modelos ARM e Azure CLI](deploy-cli.md)
* [Implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md)
* [Implementar recursos com modelos ARM e AZure Resource Manager REST API](deploy-rest.md)
* [Use um botão de implementação para implementar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implementar modelos ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de subscrição, deve fornecer uma localização para a implementação. A localização da implantação é separada da localização dos recursos que implementa. A localização da implantação especifica onde armazenar dados de implantação. [O grupo de gestão](deploy-to-management-group.md) e as implantações de [inquilinos](deploy-to-tenant.md) também requerem uma localização. Para implementações [de grupos](deploy-to-resource-group.md) de recursos, a localização do grupo de recursos é usada para armazenar os dados de implantação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome predefinido é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo denominado **azuredeploy.jscria** um nome de implementação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não é possível criar uma implantação num local quando há uma implantação existente com o mesmo nome num local diferente. Por exemplo, se criar uma implementação de subscrição com o nome **implantado1** em **central,** não pode mais tarde criar outra implantação com o nome **de implantação1,** mas uma localização de **Westus**. Se obter o código de erro `InvalidDeploymentLocation` , utilize um nome diferente ou o mesmo local que a colocação anterior para esse nome.

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao implementar uma subscrição, pode mobilizar recursos para:

* a subscrição-alvo da operação
* qualquer subscrição no inquilino
* grupos de recursos dentro da subscrição ou outras subscrições
* o inquilino para a assinatura

Um [recurso de extensão](scope-extension-resources.md) pode ser traçado para um alvo diferente do alvo de implantação.

O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Esta secção mostra como especificar diferentes âmbitos. Você pode combinar estes diferentes âmbitos em um único modelo.

### <a name="scope-to-target-subscription"></a>Âmbito de subscrição de destino

Para implantar recursos na subscrição-alvo, adicione esses recursos à secção de recursos do modelo.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Para exemplos de implantação na subscrição, consulte [Criar grupos de recursos](#create-resource-groups) e atribuir [definição de política](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Âmbito de outra subscrição

Para implementar recursos para uma subscrição diferente da subscrição da operação, adicione uma implementação aninhada. Desloque a `subscriptionId` propriedade para o ID da subscrição para a que pretende implementar. Desa parte a `location` propriedade para a implantação aninhada.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Âmbito para grupo de recursos

Para implementar recursos para um grupo de recursos dentro da subscrição, adicione uma implantação aninhada e inclua a `resourceGroup` propriedade. No exemplo seguinte, a implantação aninhada visa um grupo de recursos chamado `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Para um exemplo de implantação para um grupo de recursos, consulte [Criar grupo de recursos e recursos](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Âmbito para inquilino

Pode criar recursos no arrendatário definindo o `scope` conjunto para `/` . O utilizador que implementa o modelo deve ter o [acesso necessário para implantar no arrendatário](deploy-to-tenant.md#required-access).

Pode utilizar uma implantação aninhada `scope` e `location` definida.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Ou, pode definir o âmbito `/` para alguns tipos de recursos, como grupos de gestão.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

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
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
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
