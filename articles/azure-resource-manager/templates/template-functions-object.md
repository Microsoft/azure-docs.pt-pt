---
title: Funções do modelo - objetos
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com objetos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2f77cd85802a2ecb3670cfc6d6b36e5e852fb2a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231327"
---
# <a name="object-functions-for-arm-templates"></a>Funções de objeto para modelos ARM

O Gestor de Recursos fornece várias funções para trabalhar com objetos no seu modelo Azure Resource Manager (ARM).

* [contém](#contains)
* [vazio](#empty)
* [cruzamento](#intersection)
* [json](#json)
* [comprimento](#length)
* [união](#union)

## <a name="contains"></a>contém

`contains(container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave, ou uma corda contém um substring. A comparação de cordas é sensível a casos. No entanto, ao testar se um objeto contém uma chave, a comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |matriz, objeto ou corda |O valor que contém o valor a encontrar. |
| itemToFind |Sim |corda ou int |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**É verdade** se o item for encontrado; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) de exemplo mostra como usar contém com diferentes tipos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| cordaTrue | Booleano | Verdadeiro |
| cadeiaFalso | Booleano | Falso |
| objetoVerdadeiro | Booleano | Verdadeiro |
| objetoFalso | Booleano | Falso |
| arrayTrue | Booleano | Verdadeiro |
| arrayFalso | Booleano | Falso |

## <a name="empty"></a>vazio

`empty(itemToTest)`

Determina se uma matriz, objeto ou corda está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou corda |O valor para verificar se está vazio. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o valor estiver vazio; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) de exemplo seguinte verifica se uma matriz, objeto e corda estão vazios.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| matrizVazio | Booleano | Verdadeiro |
| objetoVazio | Booleano | Verdadeiro |
| cordaVazio | Booleano | Verdadeiro |

## <a name="intersection"></a>cruzamento

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

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) de exemplo mostra como usar a intersecção com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| objetoSaída | Objeto | {"um": "a", "três": "c"} |
| arrayOutput | Matriz | ["dois", "três"] |

## <a name="json"></a>json

`json(arg1)`

Devolve um objeto JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |string |O valor para converter para JSON. |

### <a name="return-value"></a>Valor devolvido

O objeto JSON da corda especificada ou um objeto vazio quando for especificado **nulo.**

### <a name="remarks"></a>Observações

Se precisar de incluir um valor de parâmetro ou variável no objeto JSON, utilize a função [concat](template-functions-string.md#concat) para criar a cadeia que passa para a função.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) de exemplo seguinte mostra como usar a função json. Note que pode passar numa corda que represente o objeto ou usar **nulo** quando não é necessário qualquer valor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| jsonOutput1 | Booleano | Verdadeiro |
| jsonOutput2 | Objeto | {"a": "b"} |
| paramOutput | Objeto | {"a": "valor de demonstração"}

## <a name="length"></a>length

`length(arg1)`

Devolve o número de elementos numa matriz, caracteres numa corda ou propriedades de nível raiz num objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz, corda ou objeto |A matriz a utilizar para obter o número de elementos, a corda a utilizar para obter o número de caracteres, ou o objeto a usar para obter o número de propriedades de nível raiz. |

### <a name="return-value"></a>Valor devolvido

Um int.

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) de exemplo mostra como usar o comprimento com uma matriz e uma corda:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| objetoComprimento | int | 4 |

## <a name="union"></a>união

`union(arg1, arg2, arg3, ...)`

Devolve uma única matriz ou objeto com todos os elementos dos parâmetros. Os valores ou chaves duplicados só são incluídos uma vez.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou objeto |O primeiro valor a utilizar para juntar elementos. |
| arg2 |Sim |matriz ou objeto |O segundo valor a utilizar para juntar elementos. |
| argumentos adicionais |Não |matriz ou objeto |Valores adicionais a utilizar para unir elementos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou objeto.

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) de exemplo mostra como usar a união com matrizes e objetos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| objetoSaída | Objeto | {"um": "a", "dois": "b", "três": "c2", "quatro": "d", "cinco": "e"} |
| arrayOutput | Matriz | ["um", "dois", "três", "quatro"] |

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
