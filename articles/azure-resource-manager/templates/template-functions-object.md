---
title: Funções do modelo - objetos
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com objetos.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 632e92bb798a5e8469079ef4693b7f321617f88c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977889"
---
# <a name="object-functions-for-arm-templates"></a>Funções de objeto para modelos ARM

O Gestor de Recursos fornece várias funções para trabalhar com objetos no seu modelo Azure Resource Manager (ARM).

* [contém](#contains)
* [criarObject](#createobject)
* [vazio](#empty)
* [intersecção](#intersection)
* [json](#json)
* [length](#length)
* [nulo](#null)
* [união](#union)

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave, ou uma corda contém um sub-adcção. A comparação de cordas é sensível a casos. No entanto, ao testar se um objeto contém uma chave, a comparação é insensível ao caso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |matriz, objeto ou corda |O valor que contém o valor a encontrar. |
| itemToFind |Sim |corda ou int |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**Verdade** se o item for encontrado; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) de exemplo a seguir mostra como usar contém com diferentes tipos:

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
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringTrue | Booleano | Verdadeiro |
| stringFalse | Booleano | Falso |
| objectTrue | Booleano | Verdadeiro |
| objectFalse | Booleano | Falso |
| arrayTrue | Booleano | Verdadeiro |
| arrayFalse | Booleano | Falso |

## <a name="createobject"></a>criarObject

`createObject(key1, value1, key2, value2, ...)`

Cria um objeto a partir das teclas e valores.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| chave1 |Não |cadeia |O nome da chave. |
| valor1 |Não |int, boolean, string, objeto ou matriz |O valor da chave. |
| chaves adicionais |Não |cadeia |Nomes adicionais das chaves. |
| valores adicionais |Não |int, boolean, string, objeto ou matriz |Valores adicionais para as teclas. |

A função só aceita um número par de parâmetros. Cada chave deve ter um valor correspondente.

### <a name="return-value"></a>Valor devolvido

Um objeto com cada chave e par de valor.

### <a name="example"></a>Exemplo

O exemplo a seguir cria um objeto a partir de diferentes tipos de valores.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "newObject": {
            "type": "object",
            "value": "[createObject('intProp', 1, 'stringProp', 'abc', 'boolProp', true(), 'arrayProp', createArray('a', 'b', 'c'), 'objectProp', createObject('key1', 'value1'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é um objeto nomeado `newObject` com o seguinte valor:

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>vazio

`empty(itemToTest)`

Determina se uma matriz, objeto ou corda está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou corda |O valor para verificar se está vazio. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o valor estiver vazio; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) a seguir verifica se uma matriz, objeto e corda estão vazios.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Booleano | Verdadeiro |
| objetoEmpty | Booleano | Verdadeiro |
| stringEmpty | Booleano | Verdadeiro |

## <a name="intersection"></a>intersecção

`intersection(arg1, arg2, arg3, ...)`

Devolve uma única matriz ou objeto com os elementos comuns dos parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou objeto |O primeiro valor a ser usado para encontrar elementos comuns. |
| arg2 |Sim |matriz ou objeto |O segundo valor a ser usado para encontrar elementos comuns. |
| argumentos adicionais |Não |matriz ou objeto |Valores adicionais a utilizar para encontrar elementos comuns. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou objeto com os elementos comuns.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) a seguir mostra como utilizar a intersecção com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoOutput | Objeto | {"one": "a", "três": "c"} |
| intervalo de matriz | Matriz | ["dois", "três"] |

## <a name="json"></a>json

`json(arg1)`

Converte uma cadeia JSON válida num tipo de dados JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia |O valor a converter para JSON. A corda deve ser uma corda JSON devidamente formatada. |

### <a name="return-value"></a>Valor devolvido

O tipo de dados JSON a partir da cadeia especificada, ou um valor vazio quando é especificado **nulo.**

### <a name="remarks"></a>Observações

Se precisar de incluir um valor de parâmetro ou variável no objeto JSON, utilize a função [concat](template-functions-string.md#concat) para criar a cadeia que passa para a função.

Também pode usar [nulo](#null) para obter um valor nulo.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) a seguir mostra como usar a função json. Note que pode passar **nulo** por um objeto vazio.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonEmptyObject": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "jsonString": {
            "type": "string",
            "defaultValue": "\"test\""
        },
        "jsonBoolean": {
            "type": "string",
            "defaultValue": "true"
        },
        "jsonInt": {
            "type": "string",
            "defaultValue": "3"
        },
        "jsonArray": {
            "type": "string",
            "defaultValue": "[[1,2,3 ]"
        },
        "concatValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "emptyObjectOutput": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonEmptyObject')))]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[json(parameters('jsonObject'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[json(parameters('jsonString'))]"
        },
        "booleanOutput": {
            "type": "bool",
            "value": "[json(parameters('jsonBoolean'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[json(parameters('jsonInt'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[json(parameters('jsonArray'))]"
        },
        "concatObjectOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('concatValue'), '\"}'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| outputo VazioObject | Booleano | Verdadeiro |
| objetoOutput | Objeto | {"a": "b"} |
| stringOutput | Cadeia | test |
| booleanOutput | Booleano | Verdadeiro |
| intOutput | Número inteiro | 3 |
| intervalo de matriz | Matriz | [ 1, 2, 3 ] |
| concatObjectOutput | Objeto | { "a": "valor de demonstração" } |

## <a name="length"></a>length

`length(arg1)`

Devolve o número de elementos numa matriz, caracteres numa cadeia ou propriedades de nível de raiz num objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz, corda ou objeto |A matriz a usar para obter o número de elementos, a cadeia para usar para obter o número de caracteres, ou o objeto a usar para obter o número de propriedades de nível de raiz. |

### <a name="return-value"></a>Valor devolvido

Um int.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) a seguir mostra como usar o comprimento com uma matriz e uma corda:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="null"></a>nulo

`null()`

Retorna nulos.

### <a name="parameters"></a>Parâmetros

A função nula não aceita parâmetros.

### <a name="return-value"></a>Valor devolvido

Um valor que é sempre nulo.

### <a name="example"></a>Exemplo

O exemplo a seguir utiliza a função nulo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(null())]"
        },
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| outout vazio | Booleano | Verdadeiro |

## <a name="union"></a>união

`union(arg1, arg2, arg3, ...)`

Devolve uma única matriz ou objeto com todos os elementos dos parâmetros. Os valores duplicados ou as teclas só são incluídos uma vez.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou objeto |O primeiro valor a utilizar para unir elementos. |
| arg2 |Sim |matriz ou objeto |O segundo valor a utilizar para unir elementos. |
| argumentos adicionais |Não |matriz ou objeto |Valores adicionais a utilizar para unir elementos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou objeto.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) de exemplo a seguir mostra como usar a união com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoOutput | Objeto | {"one": "a", "dois": "b", "três": "c2", "quatro": "d", "cinco": "e"} |
| intervalo de matriz | Matriz | ["um", "dois", "três", "quatro"] |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
