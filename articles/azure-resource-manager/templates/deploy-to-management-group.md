---
title: Mobilizar recursos para o grupo de gestão
description: Descreve como implantar recursos no âmbito do grupo de gestão num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 23f86d7d0b7e1f882cf3fb74adc484e0fe47db87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372430"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos ao nível do grupo de gestão

À medida que a sua organização amadurece, pode implementar um modelo de Gestor de Recursos Azure (modelo ARM) para criar recursos ao nível do grupo de gestão. Por exemplo, pode ser necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) para um grupo de gestão. Com modelos de nível de grupo de gestão, pode aplicar declarativamente políticas e atribuir funções ao nível do grupo de gestão.

## <a name="supported-resources"></a>Recursos suportados

Nem todos os tipos de recursos podem ser implantados ao nível do grupo de gestão. Esta secção lista quais os tipos de recursos suportados.

Para plantas Azure, utilize:

* [artefactos](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [plantas](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAsignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versões](/azure/templates/microsoft.blueprint/blueprints/versions)

Para políticas de Azure, utilize:

* [políticasAssinsagens](/azure/templates/microsoft.authorization/policyassignments)
* [políticasDefinições](/azure/templates/microsoft.authorization/policydefinitions)
* [políticasSetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediações](/azure/templates/microsoft.policyinsights/remediations)

Para o controlo de acesso baseado em funções Azure (Azure RBAC), utilize:

* [papéAs de assinaturas](/azure/templates/microsoft.authorization/roleassignments)
* [funçõesDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para modelos aninhados que se implementem para subscrições ou grupos de recursos, utilize:

* [implementações](/azure/templates/microsoft.resources/deployments)

Para gerir os seus recursos, utilize:

* [etiquetas](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>Esquema

O esquema que usa para implementações de grupos de gestão é diferente do esquema para implementações de grupos de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

O esquema para um ficheiro de parâmetro é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao ser implantado num grupo de gestão, pode direcionar o grupo de gestão especificado no comando de implantação ou pode selecionar outros grupos de gestão no arrendatário.

Os recursos definidos na secção de recursos do modelo são aplicados ao grupo de gestão a partir do comando de implantação.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Para direcionar outro grupo de gestão, adicione uma implantação aninhada e especifique a `scope` propriedade. Definir a `scope` propriedade para um valor no formato `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

Também pode direcionar subscrições ou grupos de recursos dentro de um grupo de gestão. O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Para direcionar uma subscrição dentro do grupo de gestão, utilize uma implantação aninhada e a `subscriptionId` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Para direcionar um grupo de recursos dentro dessa subscrição, adicione outra implantação aninhada e a `resourceGroup` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Para utilizar uma implementação de grupo de gestão para criar um grupo de recursos dentro de uma subscrição e implementar uma conta de armazenamento para esse grupo de recursos, consulte [Implementar para subscrição e grupo de recursos](#deploy-to-subscription-and-resource-group).

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de grupos de gestão são diferentes dos comandos para implantações de grupos de recursos.

Para O Azure CLI, utilize [mg de implantação az:](/cli/azure/deployment/mg#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para a Azure PowerShell, utilize [o New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para REST API, utilize [implementações - Crie no âmbito do grupo de gestão](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de grupo de gestão, deve fornecer uma localização para a implantação. A localização da implantação é separada da localização dos recursos que implementa. A localização da implantação especifica onde armazenar dados de implantação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome predefinido é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo denominado **azuredeploy.jscria** um nome de implementação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não é possível criar uma implantação num local quando há uma implantação existente com o mesmo nome num local diferente. Se obter o código de erro `InvalidDeploymentLocation` , utilize um nome diferente ou o mesmo local que a colocação anterior para esse nome.

## <a name="use-template-functions"></a>Use funções de modelo

Para implementações de grupos de gestão, existem algumas considerações importantes ao utilizar funções de modelo:

* A função [grupo de recursos()](template-functions-resource.md#resourcegroup) **não** é suportada.
* A função [de subscrição()](template-functions-resource.md#subscription) **não** é suportada.
* As funções [de referência](template-functions-resource.md#reference) e [lista são](template-functions-resource.md#list) suportadas.
* Não utilize a função [resourceId()](template-functions-resource.md#resourceid) para recursos implantados no grupo de gestão.

  Em vez disso, utilize a [função ExtensionResourceId()](template-functions-resource.md#extensionresourceid) para recursos que são implementados como extensões do grupo de gestão. As definições de política personalizada que são implementadas para o grupo de gestão são extensões do grupo de gestão.

  Para obter o ID de recurso para uma definição de política personalizada ao nível do grupo de gestão, utilize:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Utilize a função [TenantResourceId](template-functions-resource.md#tenantresourceid) para os recursos do arrendatário que estão disponíveis dentro do grupo de gestão. As definições políticas incorporadas são recursos ao nível dos inquilinos.

  Para obter o ID de recurso para uma definição de política incorporada, use:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

O exemplo a seguir mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política a nível do grupo de gestão e atribuí-la.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
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
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Implementar para grupo de subscrição e recursos

A partir de uma implementação de nível de grupo de gestão, você pode direcionar uma subscrição dentro do grupo de gestão. O exemplo a seguir cria um grupo de recursos dentro de uma subscrição e implementa uma conta de armazenamento para esse grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a atribuição de funções, consulte atribuições de [funções Add Azure utilizando modelos do Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md).
* Para um exemplo de implantação de configurações de espaço de trabalho para o Centro de Segurança Azure, consulte [deployASCwithWorkspaceSettings.jsem](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Também pode implementar modelos ao [nível de subscrição](deploy-to-subscription.md) e [ao nível do inquilino.](deploy-to-tenant.md)
