---
title: Funções do modelo - numérico
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para trabalhar com números.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: f3687581d94f80cc923614a0655da1813bd5c97b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359715"
---
# <a name="numeric-functions-for-arm-templates"></a>Funções numéricas para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com inteiros no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [adicionar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [flutuante](#float)
* [int](#int)
* [máx](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="add"></a>adicionar

`add(operand1, operand2)`

Devolve a soma dos dois inteiros fornecidos. A `add` função não é suportada em Bicep. Em vez disso, utilize o `+` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
|operand1 |Yes |int |Primeiro número a adicionar. |
|operand2 |Yes |int |Segundo número a adicionar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) a seguir adiciona dois parâmetros.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| adicionarResult | int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Devolve o índice de um ciclo de iteração.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| loopName | No | string | O nome do loop para obter a iteração. |
| offset |No |int |O número a adicionar ao valor de iteração de base zero. |

### <a name="remarks"></a>Observações

Esta função é sempre utilizada com um objeto **de cópia.** Se não for fornecido qualquer valor para **compensação,** o valor atual de iteração é devolvido. O valor de iteração começa em zero.

A propriedade **loopName** permite-lhe especificar se copyIndex está se referindo a uma iteração de recursos ou iteração de propriedade. Se não for fornecido qualquer valor para **o loopName,** utiliza-se a iteração do tipo de recurso atual. Forneça um valor para **loopName** ao iterar em uma propriedade.

Para obter mais informações sobre a utilização da cópia, consulte:

* [Iteração de recursos em modelos ARM](copy-resources.md)
* [Iteração de propriedade em modelos ARM](copy-properties.md)
* [Iteração variável em modelos ARM](copy-variables.md)
* [Iteração de saída em modelos ARM](copy-outputs.md)

### <a name="example"></a>Exemplo

O exemplo a seguir mostra um ciclo de cópia e o valor do índice incluído no nome.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Loops e `copyIndex` ainda não são implementados em Bicep.  Ver [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa o índice atual da iteração.

## <a name="div"></a>div

`div(operand1, operand2)`

Devolve a divisão de inteiros dos dois inteiros fornecidos. A `div` função não é suportada em Bicep. Em vez disso, utilize o `/` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |O número está a ser dividido. |
| operand2 |Yes |int |O número que é usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor devolvido

Um inteiro representando a divisão.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) a seguir divide um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>float

`float(arg1)`

Converte o valor num ponto flutuante. Utilize esta função apenas ao passar parâmetros personalizados para uma aplicação, como uma Aplicação Lógica. A `float` função não é suportada em Bicep.  Consulte [tipos numéricos de suporte que não os inteiros de 32bit](https://github.com/Azure/bicep/issues/486).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |corda ou int |O valor para converter para um número de ponto flutuante. |

### <a name="return-value"></a>Valor devolvido

Um número de ponto flutuante.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar o flutuador para passar parâmetros para uma App Lógica:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> A `float` função não é suportada em Bicep.  Consulte [tipos numéricos de suporte que não os inteiros de 32bit](https://github.com/Azure/bicep/issues/486).

---

## <a name="int"></a>int

`int(valueToConvert)`

Converte o valor especificado para um inteiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| valorToConvert |Yes |corda ou int |O valor para converter para um inteiro. |

### <a name="return-value"></a>Valor devolvido

Um inteiro do valor convertido.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a seguir converte o valor do parâmetro fornecido pelo utilizador para o número inteiro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

Devolve o valor máximo de uma matriz de inteiros ou de uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |matriz de inteiros, ou lista separada por vírgula de inteiros |A coleção para obter o valor máximo. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) de exemplo a seguir mostra como usar o max com uma matriz e uma lista de inteiros:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

Devolve o valor mínimo de uma matriz de inteiros ou de uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |matriz de inteiros, ou lista separada por vírgula de inteiros |A coleção para obter o valor mínimo. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) de exemplo a seguir mostra como usar min com uma matriz e uma lista de inteiros:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | int | 0 |
| intOutput | int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Devolve o restante da divisão de inteiros usando os dois inteiros fornecidos. A `mod` função não é suportada em Bicep. Em vez disso, utilize o `%` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |O número está a ser dividido. |
| operand2 |Yes |int |O número que é usado para dividir, não pode ser 0. |

### <a name="return-value"></a>Valor devolvido

Um inteiro representando o restante.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) a seguir devolve o restante de dividir um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| modResult | int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Devolve a multiplicação dos dois inteiros fornecidos. A `mul` função não é suportada em Bicep. Em vez disso, utilize o `*` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |Primeiro número a multiplicar. |
| operand2 |Yes |int |Segundo número a multiplicar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a multiplicação.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) a seguir multiplica um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| mulResult | int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Devolve a subtração dos dois inteiros fornecidos. A `sub` função não é suportada em Bicep. Em vez disso, utilize o `-` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |O número que é subtraído. |
| operand2 |Yes |int |O número que é subtraído. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a subtração.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) a seguir subtrai um parâmetro de outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| subResult | int | 4 |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte a iteração de [recursos nos modelos ARM](copy-resources.md).
