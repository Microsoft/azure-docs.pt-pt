---
title: Implantar recursos no grupo de gerenciamento
description: Descreve como implantar recursos no escopo do grupo de gerenciamento em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4ba4f4d2e95c0b878e9f402fa84139ac5b351e3c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121918"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos no nível do grupo de gerenciamento

Normalmente, você implanta recursos do Azure em um grupo de recursos em sua assinatura do Azure. No entanto, você também pode criar recursos no nível do grupo de gerenciamento. Você usa implantações no nível do grupo de gerenciamento para executar ações que fazem sentido nesse nível, como a atribuição [de controle de acesso baseado em função](../../role-based-access-control/overview.md) ou aplicação de [políticas](../../governance/policy/overview.md).

No momento, para implantar modelos no nível do grupo de gerenciamento, você deve usar a API REST.

## <a name="supported-resources"></a>Recursos suportados

Você pode implantar os seguintes tipos de recursos no nível do grupo de gerenciamento:

* [implantações](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema usado para implantações de grupo de gerenciamento é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Para arquivos de parâmetro, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

O comando para implantações de grupo de gerenciamento é diferente do comando para implantações de grupo de recursos.

Para a API REST, use [implantações-criar no escopo do grupo de gerenciamento](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Local e nome da implantação

Para implantações no nível do grupo de gerenciamento, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação.

Você pode fornecer um nome para a implantação ou usar o nome de implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy. JSON** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, o local é imutável. Não é possível criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local da implantação anterior para esse nome.

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de grupo de gerenciamento, há algumas considerações importantes ao usar funções de modelo:

* **Não** há suporte para a função [resourcegroup ()](template-functions-resource.md#resourcegroup) .
* **Não** há suporte para a função [Subscription ()](template-functions-resource.md#subscription) .
* Há suporte para a função [ResourceId ()](template-functions-resource.md#resourceid) . Use-o para obter a ID de recurso para recursos que são usados em implantações no nível do grupo de gerenciamento. Por exemplo, obtenha a ID de recurso para uma definição de política com `resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))`. Ele retorna a ID do recurso no formato `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`.
* Há suporte para as funções [Reference ()](template-functions-resource.md#reference) e [list ()](template-functions-resource.md#list) .

## <a name="create-policies"></a>Criar políticas

### <a name="define-policy"></a>Definir política

O exemplo a seguir mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política no nível do grupo de gerenciamento.

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

### <a name="assign-policy"></a>Atribuir política

O exemplo a seguir atribui uma definição de política existente ao grupo de gerenciamento. Se a política usar parâmetros, forneça-os como um objeto. Se a política não usar parâmetros, use o objeto vazio padrão.

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

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como atribuir funções, consulte [gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de espaço de trabalho para a central de segurança do Azure, consulte [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte Criando [modelos](template-syntax.md). 
* Para obter uma lista das funções disponíveis em um modelo, consulte [funções de modelo](template-functions.md).