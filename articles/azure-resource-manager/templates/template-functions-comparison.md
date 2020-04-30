---
title: Funções do modelo - comparação
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para comparar valores.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 15afc4d721c6577de9fe3e78483fdbfae5b493c6
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203782"
---
# <a name="comparison-functions-for-arm-templates"></a>Funções de comparação para modelos ARM

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos de Gestor de Recursos Azure (ARM).

* [coalesce](#coalesce)
* [iguais](#equals)
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
| arg1 |Sim |int, corda, matriz ou objeto |O primeiro valor a testar o nulo. |
| args adicional |Não |int, corda, matriz ou objeto |Valores adicionais para testar por nulo. |

### <a name="return-value"></a>Valor devolvido

O valor dos primeiros parâmetros não nulos, que podem ser uma cadeia, int, matriz ou objeto. Nulo se todos os parâmetros forem nulos.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) de exemplo seguinte mostra a saída de diferentes usos de coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| cadeiaOutput | String | predefinição |
| intOutput | int | 1 |
| objetoSaída | Objeto | {"primeiro": "padrão"} |
| arrayOutput | Matriz | [1] |
| vazioSaída | Booleano | Verdadeiro |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Verifica se dois valores são iguais um ao outro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, corda, matriz ou objeto |O primeiro valor para verificar a igualdade. |
| arg2 |Sim |int, corda, matriz ou objeto |O segundo valor para verificar a igualdade. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se os valores forem iguais; caso contrário, **Falso.**

### <a name="remarks"></a>Observações

A função é igual `condition` a ser frequentemente usada com o elemento para testar se um recurso é implantado.

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

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) de exemplo seguinte verifica diferentes tipos de valores para a igualdade. Todos os valores padrão retornar verdadeiros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| checkInts | Booleano | Verdadeiro |
| verificarStrings | Booleano | Verdadeiro |
| verificarArrays | Booleano | Verdadeiro |
| verificarObjetos | Booleano | Verdadeiro |

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) de exemplo seguinte [não](template-functions-logical.md#not) utiliza com **iguais**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Verifica se o primeiro valor é superior ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou corda |O primeiro valor para uma maior comparação. |
| arg2 |Sim |int ou corda |O segundo valor para uma maior comparação. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for superior ao segundo valor; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) de exemplo seguinte verifica se o valor é maior que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| checkInts | Booleano | Falso |
| verificarStrings | Booleano | Verdadeiro |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou corda |O primeiro valor para a comparação maior ou igual. |
| arg2 |Sim |int ou corda |O segundo valor para a comparação maior ou igual. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for maior ou igual ao segundo valor; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) de exemplo seguinte verifica se um valor é maior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| checkInts | Booleano | Falso |
| verificarStrings | Booleano | Verdadeiro |

## <a name="less"></a>less

`less(arg1, arg2)`

Verifica se o primeiro valor é inferior ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou corda |O primeiro valor para menos comparação. |
| arg2 |Sim |int ou corda |O segundo valor para menos comparação. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for inferior ao segundo valor; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) de exemplo seguinte verifica se um valor é menor que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| checkInts | Booleano | Verdadeiro |
| verificarStrings | Booleano | Falso |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é inferior ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou corda |O primeiro valor para a comparação menos ou igual. |
| arg2 |Sim |int ou corda |O segundo valor para a comparação menos ou igual. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o primeiro valor for inferior ou igual ao segundo valor; caso contrário, **Falso.**

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) de exemplo seguinte verifica se um valor é inferior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| checkInts | Booleano | Verdadeiro |
| verificarStrings | Booleano | Falso |

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
