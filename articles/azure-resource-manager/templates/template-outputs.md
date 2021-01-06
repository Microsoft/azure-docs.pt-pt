---
title: Saídas em modelos
description: Descreve como definir valores de saída num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 9e4ac134e9c1864bca8dd56c3a6e2311d0328d7d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934735"
---
# <a name="outputs-in-arm-templates"></a>Saídas em modelos ARM

Este artigo descreve como definir os valores de saída no seu modelo de Gestor de Recursos Azure (modelo ARM). Utiliza-se saídas quando precisa de devolver os valores dos recursos implantados.

O formato de cada valor de saída deve corresponder a um dos tipos de [dados](template-syntax.md#data-types).

## <a name="define-output-values"></a>Definir valores de saída

O exemplo a seguir mostra como devolver o ID do recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Saída condicional

Na secção saídas, pode devolver um valor condicionalmente. Normalmente, usa-se a condição nas saídas quando se implanta um recurso [condicionalmente.](conditional-resource-deployment.md) O exemplo a seguir mostra como devolver condicionalmente o ID do recurso para um endereço IP público com base na implementação de um novo:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para um exemplo simples de saída condicional, consulte [o modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Número dinâmico de saídas

Em alguns cenários, não sabe o número de casos de um valor que precisa de devolver ao criar o modelo. Pode devolver um número variável de valores utilizando o `copy` elemento.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Para obter mais informações, consulte [a iteração de saída nos modelos ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modelos ligados

Para recuperar o valor de saída de um modelo ligado, utilize a função [de referência](template-functions-resource.md#reference) no modelo principal. A sintaxe no modelo dos pais é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não pode incluir um traço.

O exemplo a seguir mostra como definir o endereço IP num balanceador de carga recuperando um valor de um modelo ligado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível utilizar a `reference` função na secção de saídas de um [modelo aninhado](linked-templates.md#nested-template). Para devolver os valores de um recurso implantado num modelo aninhado, converta o seu modelo aninhado num modelo ligado.

## <a name="get-output-values"></a>Obtenha valores de saída

Quando a implementação tiver sucesso, os valores de saída são automaticamente devolvidos nos resultados da implantação.

Para obter valores de saída do histórico de implementação, pode utilizar o script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos que se seguem demonstram cenários para a utilização de saídas.

|Modelo  |Descrição  |
|---------|---------|
|[Copiar variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis e saídas complexas desses valores. Não mobiliza recursos. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e produz o ID do recurso. |
|[Equilibrador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Utiliza o ID do recurso na saída ao criar o equilibrador de carga. |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
