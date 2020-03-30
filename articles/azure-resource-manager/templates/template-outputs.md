---
title: Saídas em modelos
description: Descreve como definir valores de saída num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460029"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Saídas no modelo de Gestor de Recursos Azure

Este artigo descreve como definir valores de saída no seu modelo de Gestor de Recursos Azure. Utiliza-se as saídas quando é necessário devolver valores dos recursos implantados.

## <a name="define-output-values"></a>Definir valores de saída

O exemplo que se segue mostra como devolver o ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Saída condicional

Na secção de saídas, pode devolver condicionalmente um valor. Normalmente, usa-se a condição nas saídas quando se implanta um recurso [condicionalmente.](conditional-resource-deployment.md) O exemplo que se segue mostra como devolver condicionalmente o ID de recurso para um endereço IP público com base no facto de um novo ter sido implementado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para um simples exemplo de saída condicional, consulte o modelo de [saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Número dinâmico de saídas

Em alguns cenários, você não sabe o número de casos de um valor que precisa devolver ao criar o modelo. Pode devolver um número variável de valores utilizando o elemento **de cópia.**

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

Para mais informações, consulte a [iteração de saídas nos modelos do Gestor](copy-outputs.md)de Recursos Azure .

## <a name="linked-templates"></a>Modelos ligados

Para recuperar o valor de saída a partir de um modelo ligado, utilize a função [de referência](template-functions-resource.md#reference) no modelo dos pais. A sintaxe no modelo dos pais é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não pode incluir um traço.

O exemplo seguinte mostra como definir o endereço IP num equilibrante de carga, recuperando um valor de um modelo ligado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não pode utilizar `reference` a função na secção de saídas de um [modelo aninhado](linked-templates.md#nested-template). Para devolver os valores de um recurso implantado num modelo aninhado, converta o seu modelo aninhado num modelo ligado.

## <a name="get-output-values"></a>Obter valores de saída

Quando a implementação for bem sucedida, os valores de saída são automaticamente devolvidos nos resultados da implantação.

Para obter valores de saída a partir do histórico de implementação, pode usar o script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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
|[Copiar variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e produz esses valores. Não distribui recursos. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e produz o ID do recurso. |
|[Equilibrador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Ligações ao modelo anterior. Utiliza o ID de recurso na saída ao criar o equilibrador de carga. |

## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para saídas, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)
