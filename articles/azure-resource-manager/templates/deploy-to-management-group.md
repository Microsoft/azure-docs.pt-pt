---
title: Mobilizar recursos para o grupo de gestão
description: Descreve como implementar recursos no âmbito do grupo de gestão num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460318"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos ao nível do grupo de gestão

À medida que a sua organização amadurece, poderá ter de definir e atribuir [políticas](../../governance/policy/overview.md) ou [controlos de acesso baseados em papéis](../../role-based-access-control/overview.md) para um grupo de gestão. Com modelos de nível de grupo de gestão, pode aplicar declarativamente políticas e atribuir funções ao nível do grupo de gestão.

## <a name="supported-resources"></a>Recursos suportados

Pode implementar os seguintes tipos de recursos a nível do grupo de gestão:

* [implementações](/azure/templates/microsoft.resources/deployments) - para modelos aninhados que se implantam em subscrições ou grupos de recursos.
* [políticasAtribuis](/azure/templates/microsoft.authorization/policyassignments)
* [definições políticas](/azure/templates/microsoft.authorization/policydefinitions)
* [definições políticasSetDefinições](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definições de papel](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema que usa para implementações de grupos de gestão é diferente do esquema para implementações de grupos de recursos.

Para os modelos, utilize:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

O esquema para um ficheiro de parâmetros é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para destacamentos de grupos de gestão são diferentes dos comandos para implantações de grupos de recursos.

Para o Azure CLI, utilize [az deployment mg criar:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para a Azure PowerShell, utilize a [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para a API REST, utilize [implementações - Crie no âmbito do grupo](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)de gestão .

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de grupo de gestão, você deve fornecer um local para a implementação. A localização da implantação é separada da localização dos recursos que implementa. O local de implementação especifica onde armazenar dados de implementação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome padrão é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não se pode criar uma implantação num local quando existe uma implantação existente com o mesmo nome num local diferente. Se obtê-lo o código `InvalidDeploymentLocation`de erro, utilize um nome diferente ou o mesmo local que a implementação anterior para esse nome.

## <a name="use-template-functions"></a>Funções de modelo de utilização

Para implementações de grupos de gestão, existem algumas considerações importantes ao utilizar funções de modelo:

* A função de [recurso Group()](template-functions-resource.md#resourcegroup) **não** é suportada.
* A função [de subscrição()](template-functions-resource.md#subscription) **não** é suportada.
* As funções de [referência](template-functions-resource.md#reference) e [lista](template-functions-resource.md#list) são suportadas.
* A função [resourceId()](template-functions-resource.md#resourceid) é suportada. Use-o para obter o ID de recursos para recursos que são usados em implementações de nível de grupo de gestão. Não forneça um valor para o parâmetro do grupo de recursos.

  Por exemplo, para obter o ID de recurso para uma definição de política, use:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  O ID de recurso devolvido tem o seguinte formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Criar políticas

### <a name="define-policy"></a>Definir política

O exemplo que se segue mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política a nível de grupo de gestão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
    }
  ]
}
```

### <a name="assign-policy"></a>Política de atribuição

O exemplo que se segue atribui uma definição de política existente ao grupo de gestão. Se a política tiver parâmetros, forneça-os como um objeto. Se a apólice não tomar parâmetros, use o objeto vazio por defeito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Amostra de modelo

* Criar um grupo de [recursos, uma política e uma atribuição de políticas.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a atribuição de funções, consulte [Gerir o acesso aos recursos do Azure utilizando modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para um exemplo de implementação de definições de espaço de trabalho para o Centro de Segurança Azure, consulte [o deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Também pode implementar modelos ao [nível de subscrição](deploy-to-subscription.md) e [ao nível do inquilino.](deploy-to-tenant.md)
