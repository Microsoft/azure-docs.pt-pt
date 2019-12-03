---
title: Saídas em modelos
description: Descreve como definir valores de saída em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 61120b9941a6a20812ea046265ecbe13014d769e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689150"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Saídas no modelo de Azure Resource Manager

Este artigo descreve como definir valores de saída em seu modelo de Azure Resource Manager. Você usa saídas quando precisa retornar valores dos recursos implantados.

## <a name="define-output-values"></a>Definir valores de saída

O exemplo a seguir mostra como retornar a ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Saída condicional

Na seção de saídas, você pode retornar um valor condicionalmente. Normalmente, você usa a condição nas saídas quando [implantou condicionalmente](conditional-resource-deployment.md) um recurso. O exemplo a seguir mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obter um exemplo simples de saída condicional, consulte [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Modelos ligados

Para recuperar o valor de saída de um modelo vinculado, use a função de [referência](resource-group-template-functions-resource.md#reference) no modelo pai. A sintaxe no modelo pai é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Você não pode usar a função `reference` na seção de saídas de um [modelo aninhado](resource-group-linked-templates.md#nested-template). Para retornar os valores de um recurso implantado em um modelo aninhado, converta o modelo aninhado em um modelo vinculado.

## <a name="get-output-values"></a>Obter valores de saída

Quando a implantação for realizada com sucesso, os valores de saída serão retornados automaticamente nos resultados da implantação.

Para obter valores de saída do histórico de implantação, você pode usar o script.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para o uso de saídas.

|Modelo  |Descrição  |
|---------|---------|
|[Copiar variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e gera esses valores. Não implanta nenhum recurso. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e gera a ID do recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Usa a ID do recurso na saída ao criar o balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
