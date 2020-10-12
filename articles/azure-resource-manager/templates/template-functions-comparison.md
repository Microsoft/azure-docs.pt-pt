---
title: Funções do modelo - comparação
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para comparar valores.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 01d66f43cf73dcc9228118db5a9b6149b19ee66d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677836"
---
# <a name="comparison-functions-for-arm-templates"></a>Funções de comparação para modelos ARM

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos Azure Resource Manager (ARM).

* [coalesce](#coalesce)
* [é igual a](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Devolve o primeiro valor não nulo dos parâmetros. Cordas vazias, matrizes vazias e objetos vazios não são nulos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, matriz ou objeto |O primeiro valor a testar para nulo. |
| args adicionais |Não |int, string, matriz ou objeto |Valores adicionais para testar para nulo. |

### <a name="return-value"></a>Valor devolvido

O valor dos primeiros parâmetros não nulos, que podem ser uma corda, int, matriz ou objeto. Nulo se todos os parâmetros forem nulos.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) a seguir mostra a saída de diferentes usos do coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | Cadeia | predefinição |
| intOutput | int | 1 |
| objetoOutput | Objeto | {"first": "default"} |
| intervalo de matriz | Matriz |  [1] |
| outout vazio | Booleano | Verdadeiro |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Verifica se dois valores são iguais uns aos outros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, matriz ou objeto |O primeiro valor a verificar a igualdade. |
| arg2 |Sim |int, string, matriz ou objeto |O segundo valor para verificar a igualdade. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se os valores forem iguais; caso contrário, **Falso**.

### <a name="remarks"></a>Observações

A função de iguais é frequentemente usada com o `condition` elemento para testar se um recurso é implantado.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) a seguir verifica diferentes tipos de valores para a igualdade. Todos os valores predefinidos devolvem True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkints | Booleano | Verdadeiro |
| checkStrings | Booleano | Verdadeiro |
| checkArrays | Booleano | Verdadeiro |
| checkObjects | Booleano | Verdadeiro |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir [não](template-functions-logical.md#not) usa com **iguais**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Booleano | Verdadeiro |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Verifica se o primeiro valor é maior do que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor para uma maior comparação. |
| arg2 |Sim |int ou string |O segundo valor para uma maior comparação. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for maior do que o segundo valor; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) a seguir verifica se o valor de um valor é maior do que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkints | Booleano | Falso |
| checkStrings | Booleano | Verdadeiro |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é superior ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor para a comparação maior ou igual. |
| arg2 |Sim |int ou string |O segundo valor para a comparação maior ou igual. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for superior ou igual ao segundo valor; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) a seguir verifica se o valor de um valor é maior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkints | Booleano | Falso |
| checkStrings | Booleano | Verdadeiro |

## <a name="less"></a>less

`less(arg1, arg2)`

Verifica se o primeiro valor é inferior ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor para menos comparação. |
| arg2 |Sim |int ou string |O segundo valor para menos comparação. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for inferior ao segundo valor; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) a seguir verifica se o valor de um valor é menor do que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkints | Booleano | Verdadeiro |
| checkStrings | Booleano | Falso |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é inferior ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor para a comparação menos ou igual. |
| arg2 |Sim |int ou string |O segundo valor para a comparação menos ou igual. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for inferior ou igual ao segundo valor; caso contrário, **Falso**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) a seguir verifica se o valor de um valor é inferior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkints | Booleano | Verdadeiro |
| checkStrings | Booleano | Falso |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
