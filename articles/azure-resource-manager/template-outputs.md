---
title: Saídas em modelos
description: Descreve como definir valores de saída em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: b4c652d71436202b9b6e551f9c582e5c98508259
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149180"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Saídas no modelo de Azure Resource Manager

Este artigo descreve como definir valores de saída em seu modelo de Azure Resource Manager. Você usa saídas quando precisa retornar valores dos recursos implantados.

## <a name="define-output-values"></a>Definir valores de saída

O exemplo seguinte mostra como devolver o ID de recurso para um endereço IP público:

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

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não pode incluir um traço.

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível utilizar o `reference` função na secção de saídas de um [modelo aninhado](resource-group-linked-templates.md#nested-template). Para devolver os valores para um recurso implementado num modelo aninhado, converta seu modelo aninhado para um modelo ligado.

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
|[Copie as variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexo e produz esses valores. Não implemente todos os recursos. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e devolve o ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Ligações para o modelo anterior. Utiliza o ID de recurso na saída, ao criar o Balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter recomendações sobre como criar saídas, consulte [Best Practices-Outputs](template-best-practices.md#outputs).
