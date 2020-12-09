---
title: Funções do modelo - arrays
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para trabalhar com matrizes.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 40a6815bb10ce9725405d68498b9a554706f3af8
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920549"
---
# <a name="array-functions-for-arm-templates"></a>Funções de matriz para modelos ARM

O Gestor de Recursos fornece várias funções para trabalhar com matrizes no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [matriz](#array)
* [concat](#concat)
* [contém](#contains)
* [criarArray](#createarray)
* [vazio](#empty)
* [primeiro](#first)
* [intersecção](#intersection)
* [último](#last)
* [length](#length)
* [máx](#max)
* [min](#min)
* [gama](#range)
* [saltar](#skip)
* [take](#take)
* [união](#union)

Para obter uma série de valores de cordas delimitados por um valor, consulte [split](template-functions-string.md#split).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="array"></a>matriz

`array(convertToArray)`

Converte o valor numa matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| converterToArray |Sim |int, string, matriz ou objeto |O valor para converter para uma matriz. |

### <a name="return-value"></a>Valor devolvido

Uma matriz.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar a função de matriz com diferentes tipos.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "intToConvert": {
      "type": "int",
      "defaultValue": 1
    },
    "stringToConvert": {
      "type": "string",
      "defaultValue": "efgh"
    },
    "objectToConvert": {
      "type": "object",
      "defaultValue": {
        "a": "b",
        "c": "d"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "intOutput": {
      "type": "array",
      "value": "[array(parameters('intToConvert'))]"
    },
    "stringOutput": {
      "type": "array",
      "value": "[array(parameters('stringToConvert'))]"
    },
    "objectOutput": {
      "type": "array",
      "value": "[array(parameters('objectToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intOutput | Matriz |  [1] |
| stringOutput | Matriz | ["efgh"] |
| objetoOutput | Matriz | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combina várias matrizes e devolve a matriz concatenated, ou combina múltiplos valores de cordas e devolve a corda concatenated.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou corda |A primeira matriz ou corda para a concatenação. |
| argumentos adicionais |Não |matriz ou corda |Matrizes adicionais ou cordas em ordem sequencial para a concatenação. |

Esta função pode assumir qualquer número de argumentos, e pode aceitar cordas ou matrizes para os parâmetros. No entanto, não é possível fornecer tanto matrizes como cordas para parâmetros. As matrizes só são concatenadas com outras matrizes.

### <a name="return-value"></a>Valor devolvido

Uma cadeia ou matriz de valores concatenados.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como combinar duas matrizes.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| regressar | Matriz | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) a seguir mostra como combinar dois valores de corda e devolver uma corda concatenated.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | String | prefixo-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave, ou uma corda contém um sub-adcção. A comparação de cordas é sensível a casos. No entanto, ao testar se um objeto contém uma chave, a comparação é insensível ao caso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |matriz, objeto ou corda |O valor que contém o valor a encontrar. |
| itemToFind |Sim |corda ou int |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**Verdade** se o item for encontrado; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar contém diferentes tipos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringTrue | Booleano | Verdadeiro |
| stringFalse | Booleano | Falso |
| objectTrue | Booleano | Verdadeiro |
| objectFalse | Booleano | Falso |
| arrayTrue | Booleano | Verdadeiro |
| arrayFalse | Booleano | Falso |

## <a name="createarray"></a>criarArray

`createArray (arg1, arg2, arg3, ...)`

Cria uma matriz a partir dos parâmetros. A `createArray` função não é suportada por Bicep.  Construa uma matriz literal utilizando `[]` .

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| args |Não |String, Inteiro, Array ou Objeto |Os valores na matriz. |

### <a name="return-value"></a>Valor devolvido

Uma matriz. Quando não são fornecidos parâmetros, devolve uma matriz vazia.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar o CreateArray com diferentes tipos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringArray": {
      "type": "array",
      "value": "[createArray('a', 'b', 'c')]"
    },
    "intArray": {
      "type": "array",
      "value": "[createArray(1, 2, 3)]"
    },
    "objectArray": {
      "type": "array",
      "value": "[createArray(parameters('objectToTest'))]"
    },
    "arrayArray": {
      "type": "array",
      "value": "[createArray(parameters('arrayToTest'))]"
    },
    "emptyArray": {
      "type": "array",
      "value": "[createArray()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `createArray()` não é apoiado por Bicep.  Construa uma matriz literal utilizando `[]` .

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringArray | Matriz | ["a", "b", "c"] |
| intArray | Matriz | [1, 2, 3] |
| objetoArray | Matriz | [{"one": "a", "two": "b", "três": "c"}] |
| matrizArray | Matriz | ["um", "dois", "três"]] |
| VazioArray | Matriz | [] |

## <a name="empty"></a>vazio

`empty(itemToTest)`

Determina se uma matriz, objeto ou corda está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou corda |O valor para verificar se está vazio. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o valor estiver vazio; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O exemplo a seguir verifica se uma matriz, objeto e corda estão vazios.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Booleano | Verdadeiro |
| objetoEmpty | Booleano | Verdadeiro |
| stringEmpty | Booleano | Verdadeiro |

## <a name="first"></a>primeiro

`first(arg1)`

Devolve o primeiro elemento da matriz, ou primeiro carácter da corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou corda |O valor para recuperar o primeiro elemento ou caráter. |

### <a name="return-value"></a>Valor devolvido

O tipo (corda, int, matriz ou objeto) do primeiro elemento numa matriz, ou o primeiro caracter de uma corda.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar a primeira função com uma matriz e corda.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | String | um |
| stringOutput | String | O |

## <a name="intersection"></a>intersecção

`intersection(arg1, arg2, arg3, ...)`

Devolve uma única matriz ou objeto com os elementos comuns dos parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou objeto |O primeiro valor a ser usado para encontrar elementos comuns. |
| arg2 |Sim |matriz ou objeto |O segundo valor a ser usado para encontrar elementos comuns. |
| argumentos adicionais |Não |matriz ou objeto |Valores adicionais a utilizar para encontrar elementos comuns. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou objeto com os elementos comuns.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar a intersecção com matrizes e objetos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoOutput | Objeto | {"one": "a", "três": "c"} |
| intervalo de matriz | Matriz | ["dois", "três"] |

## <a name="last"></a>último

`last (arg1)`

Devolve o último elemento da matriz, ou último carácter da corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou corda |O valor para recuperar o último elemento ou caráter. |

### <a name="return-value"></a>Valor devolvido

O tipo (corda, int, matriz ou objeto) do último elemento numa matriz, ou o último caracter de uma corda.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar a última função com uma matriz e uma corda.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two three')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | String | três |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Devolve o número de elementos numa matriz, caracteres numa cadeia ou propriedades de nível de raiz num objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz, corda ou objeto |A matriz a usar para obter o número de elementos, a cadeia para usar para obter o número de caracteres, ou o objeto a usar para obter o número de propriedades de nível de raiz. |

### <a name="return-value"></a>Valor devolvido

Um int.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar o comprimento com uma matriz e uma corda:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

Pode utilizar esta função com uma matriz para especificar o número de iterações ao criar recursos. No exemplo seguinte, o site de **parâmetrosNames** refere-se a uma série de nomes a utilizar ao criar os web sites.

# <a name="json"></a>[JSON](#tab/json)

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Loops ainda não são implementados em Bicep.  Ver [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

Para obter mais informações sobre a utilização desta função com uma matriz, consulte [a iteração de recursos nos modelos ARM](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Devolve o valor máximo de uma matriz de inteiros ou de uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros, ou lista separada por vírgula de inteiros |A coleção para obter o valor máximo. |

### <a name="return-value"></a>Valor devolvido

Um int representando o valor máximo.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar o max com uma matriz e uma lista de inteiros:

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

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min(arg1)`

Devolve o valor mínimo de uma matriz de inteiros ou de uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros, ou lista separada por vírgula de inteiros |A coleção para obter o valor mínimo. |

### <a name="return-value"></a>Valor devolvido

Um int representando o valor mínimo.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar min com uma matriz e uma lista de inteiros:

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

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | int | 0 |
| intOutput | int | 0 |

## <a name="range"></a>gama

`range(startIndex, count)`

Cria uma variedade de inteiros a partir de um inteiro inicial e contendo uma série de itens.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| startIndex |Sim |int |O primeiro inteiro na matriz. A soma do startIndex e da contagem não deve ser superior a 2147483647. |
| count |Sim |int |O número de inteiros na matriz. Deve ser inteiro não negativo até 10.000. |

### <a name="return-value"></a>Valor devolvido

Uma série de inteiros.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar a função de intervalo:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "startingInt": {
      "type": "int",
      "defaultValue": 5
    },
    "numberOfElements": {
      "type": "int",
      "defaultValue": 3
    }
  },
  "resources": [],
  "outputs": {
    "rangeOutput": {
      "type": "array",
      "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| rangeOutput | Matriz | [5, 6, 7] |

## <a name="skip"></a>saltar

`skip(originalValue, numberToSkip)`

Retorna uma matriz com todos os elementos após o número especificado na matriz, ou retorna uma cadeia com todos os caracteres após o número especificado na cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou corda |A matriz ou corda para usar para saltar. |
| numberToSkip |Sim |int |O número de elementos ou caracteres a saltar. Se este valor for 0 ou menos, todos os elementos ou caracteres do valor são devolvidos. Se for maior do que o comprimento da matriz ou da corda, é devolvida uma matriz ou corda vazia. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="example"></a>Exemplo

O exemplo a seguir ignora o número especificado de elementos na matriz e o número especificado de caracteres numa cadeia.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | Matriz | ["três"] |
| stringOutput | String | dois três |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Devolve uma matriz com o número especificado de elementos desde o início da matriz, ou uma cadeia com o número especificado de caracteres desde o início da cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou corda |A matriz ou corda para tirar os elementos. |
| numberToTake |Sim |int |O número de elementos ou caracteres a tomar. Se este valor for 0 ou menos, uma matriz ou corda vazia é devolvida. Se for maior do que o comprimento da matriz ou corda dada, todos os elementos da matriz ou da corda são devolvidos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="example"></a>Exemplo

O exemplo a seguir retira o número especificado de elementos da matriz e caracteres de uma corda.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | Matriz | ["um", "dois"] |
| stringOutput | String | em |

## <a name="union"></a>união

`union(arg1, arg2, arg3, ...)`

Devolve uma única matriz ou objeto com todos os elementos dos parâmetros. Os valores duplicados ou as teclas só são incluídos uma vez.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou objeto |O primeiro valor a utilizar para unir elementos. |
| arg2 |Sim |matriz ou objeto |O segundo valor a utilizar para unir elementos. |
| argumentos adicionais |Não |matriz ou objeto |Valores adicionais a utilizar para unir elementos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou objeto.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como utilizar a união com matrizes e objetos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoOutput | Objeto | {"one": "a", "dois": "b", "três": "c2", "quatro": "d", "cinco": "e"} |
| intervalo de matriz | Matriz | ["um", "dois", "três", "quatro"] |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
