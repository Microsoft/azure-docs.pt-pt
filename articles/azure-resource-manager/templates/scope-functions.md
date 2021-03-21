---
title: Funções do modelo em implementações de âmbito
description: Descreve como as funções do modelo são resolvidas em implementações âmbito. O âmbito pode ser um inquilino, grupos de gestão, subscrições e grupos de recursos.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492098"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Funções do modelo ARM em âmbitos de implantação

Com modelos de Gestor de Recursos Azure (modelos ARM), pode implementar em grupos de recursos, subscrições, grupos de gestão ou inquilinos. Geralmente, as [funções do modelo ARM](template-functions.md) funcionam da mesma forma para todos os âmbitos. Este artigo descreve as diferenças que existem para algumas funções dependendo do âmbito.

## <a name="supported-functions"></a>Funções suportadas

Ao implementar em diferentes âmbitos, existem algumas considerações importantes:

* A função [grupo de recursos()](template-functions-resource.md#resourcegroup) é **suportada** para implementações de grupos de recursos.
* A função [de subscrição()](template-functions-resource.md#subscription) é **suportada** para implementações de grupo de recursos e subscrição.
* As funções [de referência](template-functions-resource.md#reference) e [lista()](template-functions-resource.md#list) são **suportadas** para todos os âmbitos.
* Utilize [recursosId()](template-functions-resource.md#resourceid) para obter o ID para um recurso implantado no grupo de recursos.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Utilize a função [subscriçãoResourceId()](template-functions-resource.md#subscriptionresourceid) para obter o ID de um recurso implantado na subscrição.

  Por exemplo, para obter o ID de recurso para uma definição de política que é implantada numa subscrição, use:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Utilize a [função extensionResourceId()](template-functions-resource.md#extensionresourceid) para recursos que são implementados como extensões do grupo de gestão. As definições de política personalizada que são implementadas para o grupo de gestão são extensões do grupo de gestão.

  Para obter o ID de recurso para uma definição de política personalizada ao nível do grupo de gestão, utilize:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Utilize a função [tenantResourceId()](template-functions-resource.md#tenantresourceid) para obter o ID para um recurso implantado no inquilino. As definições políticas incorporadas são recursos ao nível dos inquilinos. Ao atribuir uma política incorporada ao nível do grupo de gestão, utilize a função TenantResourceId.

  Para obter o ID de recurso para uma definição de política incorporada, use:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Resolução de funções em âmbitos

Quando implementa para mais de um âmbito, as funções [de Grupo de Recursos ()](template-functions-resource.md#resourcegroup) e [subscrição resolvem-se](template-functions-resource.md#subscription) de forma diferente com base na forma como especifica o modelo. Quando se liga a um modelo externo, as funções resolvem-se sempre ao âmbito desse modelo. Quando você nidificar um modelo dentro de um modelo de pai, use a `expressionEvaluationOptions` propriedade para especificar se as funções resolvem para o grupo de recursos e subscrição para o modelo dos pais ou o modelo aninhado. Desa estariuse a propriedade `inner` para resolver o âmbito do modelo aninhado. Desa estariuse a propriedade `outer` para resolver o âmbito do modelo dos pais.

A tabela a seguir mostra se as funções resolvem-se para o grupo de recursos e subscrição do progenitor ou incorporado.

| Tipo de modelo | Âmbito | Resolução |
| ------------- | ----- | ---------- |
| aninhado        | exterior (padrão) | Grupo de recursos parentais |
| aninhado        | interior | Sub-grupo de recursos |
| ligado        | N/D   | Sub-grupo de recursos |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) a seguir mostra:

* modelo aninhado com âmbito padrão (exterior)
* modelo aninhado com âmbito interno
* modelo ligado

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Para testar o modelo anterior e ver os resultados, utilize o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo ARM privado com o token SAS](secure-template-with-sas-token.md).
