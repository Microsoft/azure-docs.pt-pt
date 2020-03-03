---
title: Mobilizar recursos para o grupo de gestão
description: Descreve como implementar recursos no âmbito do grupo de gestão num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 3b2eeaf2c63a50cda1a32fee94c1e5b99822075d
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228109"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos ao nível do grupo de gestão

Normalmente, você implementa recursos Azure para um grupo de recursos na sua subscrição Azure. No entanto, também pode criar recursos a nível de grupo de gestão. Utiliza-se implementações de nível de grupo de gestão para tomar medidas que façam sentido a esse nível, tais como a atribuição de controlo de [acesso baseado em papéis](../../role-based-access-control/overview.md) ou a aplicação de [políticas.](../../governance/policy/overview.md)

## <a name="supported-resources"></a>Recursos suportados

Pode implementar os seguintes tipos de recursos a nível do grupo de gestão:

* [implementações](/azure/templates/microsoft.resources/deployments)
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

Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para destacamentos de grupos de gestão são diferentes dos comandos para implantações de grupos de recursos.

Para a Azure PowerShell, utilize a [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment). 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

Para a API REST, utilize [implementações - Crie no âmbito do grupo](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)de gestão .

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de grupo de gestão, você deve fornecer um local para a implementação. A localização da implantação é separada da localização dos recursos que implementa. O local de implementação especifica onde armazenar dados de implementação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome padrão é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não se pode criar uma implantação num local quando existe uma implantação existente com o mesmo nome num local diferente. Se obtê-lo com o código de erro `InvalidDeploymentLocation`, utilize um nome diferente ou o mesmo local que a implementação anterior para esse nome.

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
* Para aprender sobre a criação de modelos de Gestor de Recursos Azure, consulte [os modelos de autor.](template-syntax.md)
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](template-functions.md).