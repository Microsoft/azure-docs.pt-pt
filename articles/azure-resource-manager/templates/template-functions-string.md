---
title: Funções do modelo - cadeia
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para trabalhar com cordas.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cff1424562b45bc722f87fa3ec896c1c641ee758
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108847"
---
# <a name="string-functions-for-arm-templates"></a>Funções de corda para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com cordas no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contém](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [vazio](#empty)
* [terminaWith](#endswith)
* [primeiro](#first)
* [formato](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [json](#json)
* [último](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [substituir](#replace)
* [saltar](#skip)
* [divisão](#split)
* [começacom](#startswith)
* [string](#string)
* [substring](#substring)
* [tomar](#take)
* [toLower](#tolower)
* [toupper](#toupper)
* [guarnição](#trim)
* [exclusivosastragem](#uniquestring)
* [uri](#uri)
* [uriComponente](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Devolve a representação base64 da cadeia de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |string |O valor para voltar como uma representação base64. |

### <a name="return-value"></a>Valor devolvido

Uma corda contendo a representação base64.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir mostra como utilizar a função base64.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Outout | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converte uma representação base64 num objeto JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |A representação base64 para converter-se a um objeto JSON. |

### <a name="return-value"></a>Valor devolvido

Um objeto JSON.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir utiliza a função base64ToJson para converter um valor base64:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Outout | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Converte uma representação base64 numa corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |A representação base64 para converter-se a uma corda. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do valor base 64 convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir utiliza a função base64ToString para converter um valor base64:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Outout | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combina múltiplos valores de cordas e devolve a corda concatenated, ou combina várias matrizes e devolve a matriz concatenated.

Para simplificar a concatenação das cordas, bicep suporta uma sintaxe de interpolação de [cordas.](https://en.wikipedia.org/wiki/String_interpolation#)

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |cadeia ou matriz |A primeira corda ou matriz para concatenação. |
| argumentos adicionais |No |cadeia ou matriz |Cordas ou matrizes adicionais em ordem sequencial para a concatenação. |

Esta função pode assumir qualquer número de argumentos, e pode aceitar cordas ou matrizes para os parâmetros. No entanto, não é possível fornecer tanto matrizes como cordas para parâmetros. As cordas só são concatenadas com outras cordas.

### <a name="return-value"></a>Valor devolvido

Uma cadeia ou matriz de valores concatenados.

### <a name="examples"></a>Exemplos

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

ou

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | String | prefixo-5yj4yjf5mbg72 |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) a seguir mostra como combinar duas matrizes.

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

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave, ou uma corda contém um sub-adcção. A comparação de cordas é sensível a casos. No entanto, ao testar se um objeto contém uma chave, a comparação é insensível ao caso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| contentor |Yes |matriz, objeto ou corda |O valor que contém o valor a encontrar. |
| itemToFind |Yes |corda ou int |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**Verdade** se o item for encontrado; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) de exemplo a seguir mostra como usar contém com diferentes tipos:

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converte um valor para um dado URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |string |O valor para converter para um dado URI. |

### <a name="return-value"></a>Valor devolvido

Uma corda formatada como um URI de dados.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) de exemplo a seguir converte um valor para um URI de dados e converte um URI de dados numa cadeia:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados:texto/planície;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Olá, mundo! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Converte um valor formatado por URI de dados para uma cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| dadosUriToConvert |Yes |string |O valor URI de dados para converter. |

### <a name="return-value"></a>Valor devolvido

Uma corda contendo o valor convertido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) de exemplo a seguir converte um valor para um URI de dados e converte um URI de dados numa cadeia:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados:texto/planície;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Olá, mundo! |

## <a name="empty"></a>vazio

`empty(itemToTest)`

Determina se uma matriz, objeto ou corda está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |matriz, objeto ou corda |O valor para verificar se está vazio. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o valor estiver vazio; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) a seguir verifica se uma matriz, objeto e corda estão vazios.

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina se uma corda termina com um valor. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |O valor que contém o item a encontrar. |
| stringToFind |Yes |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**É verdade** se o último personagem ou caracteres da corda corresponder ao valor; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) de exemplo a seguir mostra como usar o arranqueCom e terminaCom as funções:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Booleano | Verdadeiro |
| startsCapTrue | Booleano | Verdadeiro |
| começaFalse | Booleano | Falso |
| terminaTrue | Booleano | Verdadeiro |
| terminaCapTrue | Booleano | Verdadeiro |
| terminaFalse | Booleano | Falso |

## <a name="first"></a>primeiro

`first(arg1)`

Devolve o primeiro personagem da corda, ou primeiro elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |matriz ou corda |O valor para recuperar o primeiro elemento ou caráter. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do primeiro personagem, ou o tipo (corda, int, matriz ou objeto) do primeiro elemento de uma matriz.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) a seguir mostra como usar a primeira função com uma matriz e corda.

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

## <a name="format"></a>formato

`format(formatString, arg1, arg2, ...)`

Cria uma cadeia formatada a partir de valores de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| formatString | Yes | string | A cadeia de formato composto. |
| arg1 | Yes | corda, inteiro, ou boolean | O valor a incluir na cadeia formatada. |
| argumentos adicionais | No | corda, inteiro, ou boolean | Valores adicionais para incluir na cadeia formatada. |

### <a name="remarks"></a>Observações

Utilize esta função para formatar uma corda no seu modelo. Utiliza as mesmas opções de formatação que o método [System.String.Format](/dotnet/api/system.string.format) em .NET.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra como utilizar a função de formato.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| formatoTest | String | Olá, Utilizador. Número formatado: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Cria um valor no formato de um identificador globalmente único com base nos valores fornecidos como parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |O valor utilizado na função hash para criar o GUID. |
| parâmetros adicionais, conforme necessário |No |string |Pode adicionar o número de cordas necessárias para criar o valor que especifica o nível de singularidade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa de criar um valor no formato de um identificador globalmente único. Fornece-se valores de parâmetros que limitam o âmbito de singularidade para o resultado. Pode especificar se o nome é exclusivo para subscrição, grupo de recursos ou implementação.

O valor devolvido não é uma corda aleatória, mas sim o resultado de uma função de haxixe nos parâmetros. O valor devolvido tem 36 caracteres de comprimento. Não é globalmente único. Para criar um novo GUID que não seja baseado nesse valor de haxixe dos parâmetros, use a nova função [Guia.](#newguid)

Os exemplos a seguir mostram como usar o guia para criar um valor único para níveis comumente usados.

Âmbito único à subscrição

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Âmbito único para grupo de recursos

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Âmbito único para implantação de um grupo de recursos

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Valor devolvido

Uma corda contendo 36 caracteres no formato de um identificador globalmente único.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) de exemplo a seguir de volta resulta do guia:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Devolve a primeira posição de um valor dentro de uma corda. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |O valor que contém o item a encontrar. |
| stringToFind |Yes |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a posição do item para encontrar. O valor é baseado em zero. Se o artigo não for encontrado, -1 é devolvido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a seguir mostra como utilizar as funções IndexOf e LastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstT | int | 0 |
| últimoT | int | 3 |
| firstString | int | 2 |
| últimastragem | int | 0 |
| não Confuso | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Converte uma cadeia JSON válida num tipo de dados JSON. Para mais informações, consulte [a função json](template-functions-object.md#json).

## <a name="last"></a>último

`last (arg1)`

Devolve o último personagem da corda, ou o último elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |matriz ou corda |O valor para recuperar o último elemento ou caráter. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do último personagem, ou o tipo (corda, int, matriz ou objeto) do último elemento numa matriz.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) a seguir mostra como usar a última função com uma matriz e corda.

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
output stringOutput string = last('One Two Three')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intervalo de matriz | String | três |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Devolve a última posição de um valor dentro de uma corda. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |O valor que contém o item a encontrar. |
| stringToFind |Yes |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a última posição do item para encontrar. O valor é baseado em zero. Se o artigo não for encontrado, -1 é devolvido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a seguir mostra como utilizar as funções IndexOf e LastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstT | int | 0 |
| últimoT | int | 3 |
| firstString | int | 2 |
| últimastragem | int | 0 |
| não Confuso | int | -1 |

## <a name="length"></a>length

`length(string)`

Devolve o número de caracteres numa corda, elementos numa matriz ou propriedades de nível de raiz num objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |matriz, corda ou objeto |A matriz a usar para obter o número de elementos, a cadeia para usar para obter o número de caracteres, ou o objeto a usar para obter o número de propriedades de nível de raiz. |

### <a name="return-value"></a>Valor devolvido

Um int.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) a seguir mostra como usar o comprimento com uma matriz e uma corda:

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

## <a name="newguid"></a>newGuid

`newGuid()`

Devolve um valor no formato de um identificador globalmente único. **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor predefinido de um parâmetro. A utilização desta função em qualquer outro lugar de um modelo retorna um erro. A função não é permitida noutras partes do modelo porque devolve um valor diferente cada vez que é chamado. A implantação do mesmo modelo com os mesmos parâmetros não produziria de forma fiável os mesmos resultados.

A função newGuid difere da função [de guia](#guid) porque não toma quaisquer parâmetros. Quando chama guid com o mesmo parâmetro, retorna o mesmo identificador de cada vez. Utilize o guia quando necessitar de gerar de forma fiável o mesmo GUID para um ambiente específico. Use o newGuid quando precisar de um identificador diferente de cada vez, como a implantação de recursos para um ambiente de teste.

A nova função Guid utiliza a [estrutura Guid](/dotnet/api/system.guid) no Quadro .NET para gerar o identificador globalmente único.

Se utilizar a [opção de recolocar uma implementação com sucesso anterior](rollback-on-error.md), e a implementação anterior inclui um parâmetro que utiliza o newGuid, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado na implementação de reversão.

Num ambiente de teste, poderá ser necessário utilizar repetidamente recursos que apenas vivam por um curto período de tempo. Em vez de construir nomes únicos, você pode usar newGuid com [un uniqueString](#uniquestring) para criar nomes únicos.

Tenha cuidado ao recolocar um modelo que dependa da função newGuid para um valor predefinido. Quando se recoloca e não fornece um valor para o parâmetro, a função é reavaliada. Se pretender atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro a partir da implementação anterior.

### <a name="return-value"></a>Valor devolvido

Uma corda contendo 36 caracteres no formato de um identificador globalmente único.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra um parâmetro com um novo identificador.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

O exemplo a seguir utiliza a função newGuid para criar um nome único para uma conta de armazenamento. Este modelo pode funcionar para o ambiente de teste onde a conta de armazenamento existe por um curto período de tempo e não é redistribuído.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| nomeOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Devolve uma corda alinhada à direita adicionando caracteres à esquerda até atingir o comprimento total especificado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| valorToPad |Yes |corda ou int |O valor para o alinhamento certo. |
| totalLength |Yes |int |O número total de caracteres na cadeia devolvida. |
| afiliaçãoCharacter |No |personagem único |O carácter a utilizar para o acolchoamento esquerdo até atingir o comprimento total. O valor predefinido é um espaço. |

Se a corda original for maior do que o número de caracteres a remar, não são adicionados caracteres.

### <a name="return-value"></a>Valor devolvido

Uma corda com pelo menos o número de caracteres especificados.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) a seguir mostra como apadrinhar o valor do parâmetro fornecido pelo utilizador adicionando o carácter zero até atingir o número total de caracteres.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>substituir

`replace(originalString, oldString, newString)`

Devolve uma nova corda com todas as instâncias de uma corda substituída por outra corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| originalString |Yes |string |O valor que tem todos os casos de uma corda substituída por outra corda. |
| oldString |Yes |string |A corda a ser removida da corda original. |
| newString |Yes |string |A corda para adicionar no lugar da corda removida. |

### <a name="return-value"></a>Valor devolvido

Uma corda com os caracteres substituídos.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) de exemplo a seguir mostra como remover todos os traços da corda fornecida pelo utilizador e como substituir parte da cadeia por outra corda.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>saltar

`skip(originalValue, numberToSkip)`

Retorna uma cadeia com todos os caracteres após o número especificado de caracteres, ou uma matriz com todos os elementos após o número especificado de elementos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |matriz ou corda |A matriz ou corda para usar para saltar. |
| numberToSkip |Yes |int |O número de elementos ou caracteres a saltar. Se este valor for 0 ou menos, todos os elementos ou caracteres do valor são devolvidos. Se for maior do que o comprimento da matriz ou da corda, uma matriz ou corda vazia é devolvida. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) a seguir ignora o número especificado de elementos na matriz e o número especificado de caracteres numa cadeia.

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

## <a name="split"></a>dividir

`split(inputString, delimiter)`

Devolve uma matriz de cordas que contém os sublutos da cadeia de entrada que são delimitadas pelos delimitadores especificados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |string |A corda para dividir. |
| delimitador |Yes |cadeia ou matriz de cordas |O delimiter para usar para dividir a corda. |

### <a name="return-value"></a>Valor devolvido

Uma série de cordas.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) a seguir divide a cadeia de entrada com uma vírgula, e com uma vírgula ou um ponto e vírgula.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | Matriz | ["um", "dois", "três"] |
| secondOutput | Matriz | ["um", "dois", "três"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina se uma corda começa com um valor. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |O valor que contém o item a encontrar. |
| stringToFind |Yes |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**É verdade** se o primeiro carácter ou caracteres da corda corresponder ao valor; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) de exemplo a seguir mostra como usar o arranqueCom e terminaCom as funções:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Booleano | Verdadeiro |
| startsCapTrue | Booleano | Verdadeiro |
| começaFalse | Booleano | Falso |
| terminaTrue | Booleano | Verdadeiro |
| terminaCapTrue | Booleano | Verdadeiro |
| terminaFalse | Booleano | Falso |

## <a name="string"></a>string

`string(valueToConvert)`

Converte o valor especificado para uma cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| valorToConvert |Yes | Qualquer |O valor para converter em corda. Qualquer tipo de valor pode ser convertido, incluindo objetos e matrizes. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) de exemplo a seguir mostra como converter diferentes tipos de valores em cordas:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoOutput | String | {"valueA":10,"valueB":"Texto exemplo"} |
| intervalo de matriz | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Devolve um sub-adc de substring que começa na posição de caracteres especificado e contém o número especificado de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |string |A corda original a partir da qual o sublmoão é extraído. |
| startIndex |No |int |A posição de caracteres ini por si só para o sub-adiscão. |
| length |No |int |O número de caracteres para o sub-adcção. Deve consultar uma localização dentro da cadeia. Deve ser zero ou maior. |

### <a name="return-value"></a>Valor devolvido

O sub-cordão. Ou uma corda vazia se o comprimento for zero.

### <a name="remarks"></a>Observações

A função falha quando o sub-adc de sublagem se estende para além da extremidade da corda, ou quando o comprimento é inferior a zero. O exemplo a seguir falha com o erro "Os parâmetros de índice e comprimento devem consultar-se para uma localização dentro da cadeia. O parâmetro do índice: '0', o parâmetro de comprimento: '11', o comprimento do parâmetro da corda: '10'.".

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) a seguir extrai um sublaminho de um parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| substringOutput | String | dois |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Devolve uma cadeia com o número especificado de caracteres desde o início da cadeia, ou uma matriz com o número especificado de elementos desde o início da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |matriz ou corda |A matriz ou corda para tirar os elementos. |
| numberToTake |Yes |int |O número de elementos ou caracteres a tomar. Se este valor for 0 ou menos, uma matriz ou corda vazia é devolvida. Se for maior do que o comprimento da matriz ou corda dada, todos os elementos da matriz ou da corda são devolvidos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a seguir retira o número especificado de elementos da matriz e caracteres de uma cadeia.

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

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Converte a cadeia especificada para minúscula.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |O valor para converter para minúsculas. |

### <a name="return-value"></a>Valor devolvido

A corda convertida em minúsculas.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) a seguir converte um valor de parâmetro para minúscula e para maiússão.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="toupper"></a>toupper

`toUpper(stringToChange)`

Converte a cadeia especificada para a caixa superior.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |O valor para converter em maiúsão. |

### <a name="return-value"></a>Valor devolvido

A corda convertida em maiúsão.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) a seguir converte um valor de parâmetro para minúscula e para maiússão.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="trim"></a>guarnição

`trim (stringToTrim)`

Remove todos os caracteres de espaço branco líder e trailing da cadeia especificada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |string |O valor a aparar. |

### <a name="return-value"></a>Valor devolvido

A corda sem liderar e seguir personagens de espaço branco.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) a seguir apara os caracteres do espaço branco a partir do parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| regressar | String | Um dois três |

## <a name="uniquestring"></a>exclusivosastragem

`uniqueString (baseString, ...)`

Cria uma cadeia de haxixe determinística com base nos valores fornecidos como parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |O valor usado na função hash para criar uma corda única. |
| parâmetros adicionais, conforme necessário |No |string |Pode adicionar o número de cordas necessárias para criar o valor que especifica o nível de singularidade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa de criar um nome único para um recurso. Fornece-se valores de parâmetros que limitam o âmbito de singularidade para o resultado. Pode especificar se o nome é exclusivo para subscrição, grupo de recursos ou implementação.

O valor devolvido não é uma corda aleatória, mas sim o resultado de uma função de haxixe. O valor devolvido tem 13 caracteres de comprimento. Não é globalmente único. Você pode querer combinar o valor com um prefixo da sua convenção de nomeação para criar um nome que seja significativo. O exemplo a seguir mostra o formato do valor devolvido. O valor real varia de acordo com os parâmetros fornecidos.

`tcvhiyu5h2o5o`

Os exemplos a seguir mostram como usar o UniqueString para criar um valor único para níveis comumente usados.

Âmbito único à subscrição

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Âmbito único para grupo de recursos

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Âmbito único para implantação de um grupo de recursos

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

O exemplo a seguir mostra como criar um nome único para uma conta de armazenamento baseada no seu grupo de recursos. Dentro do grupo de recursos, o nome não é único se construído da mesma forma.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Se precisar de criar um novo nome único cada vez que implementar um modelo, e não pretender atualizar o recurso, pode utilizar a função [utcNow](template-functions-date.md#utcnow) com um ExclusivoString. Pode usar esta abordagem num ambiente de teste. Por exemplo, consulte [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Valor devolvido

Uma corda contendo 13 caracteres.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) de exemplo a seguir resulta de um ensaio único:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Cria um URI absoluto combinando a baseUri e a cadeiauri relativa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |string |A corda uri base. Tenha o cuidado de observar o comportamento relativo ao manuseamento do corte de fuga ('/'), conforme descrito na sequência desta tabela.  |
| relativoUri |Yes |string |A cadeia uri relativa para adicionar à cadeia uri base. |

* Se **o BaseUri** terminar num corte de fuga, o resultado é simplesmente **baseUri** seguido **poruriuri relativo**.

* Se **a BaseUri** não acabar num corte de fuga, uma de duas coisas acontece.

   * Se **o BaseUri** não tiver cortes de todo (além do "// " próximo da frente) o resultado é simplesmente **baseUri** seguido **poruriuri relativo**.

   * Se **o BaseUri** tiver alguns cortes, mas não terminar com um corte, tudo a partir do último corte é removido da **baseUri** e o resultado é **baseUri** seguido **poruriUri**.

Eis alguns exemplos:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Para mais detalhes completos, os parâmetros **baseUri** e **RelativoUri** são resolvidos conforme especificado no [RFC 3986, secção 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Valor devolvido

Uma corda que representa o URI absoluto para a base e valores relativos.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra como construir uma ligação a um modelo aninhado com base no valor do modelo principal.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo a seguir mostra como usar uricomponent e uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componenteOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponente

`uricomponent(stringToEncode)`

Codifica um URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |string |O valor para codificar. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do valor codificado uri.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo a seguir mostra como usar uricomponent e uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componenteOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Devolve uma sequência de valor codificado URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |string |O valor codificado uri para converter-se a uma corda. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia descodificada de valor codificado URI.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo a seguir mostra como usar uricomponent e uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componenteOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para fundir vários modelos, consulte [utilizando modelos ligados e aninhados ao implementar recursos Azure](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte a iteração de [recursos nos modelos ARM](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [recursos de implantação com modelos ARM e Azure PowerShell](deploy-powershell.md).
