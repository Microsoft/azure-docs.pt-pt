---
title: Funções de modelo do Resource Manager do Azure - comparação | Documentos da Microsoft
description: Descreve as funções para utilizar um modelo Azure Resource Manager para comparar valores.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 0eafadd4049baea58aa882b3421eaa6a684a3a38
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205488"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Funções de comparação para modelos Azure Resource Manager

Resource Manager fornece várias funções para fazer comparações nos seus modelos.

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [menos](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>é igual a
`equals(arg1, arg2)`

Verifica se os dois valores iguais entre si.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para verificar a existência de igualdade. |
| arg2 |Sim |int, string, array ou object |O segundo valor a verificar a existência de igualdade. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se os valores forem iguais; caso contrário, **falso**.

### <a name="remarks"></a>Observações

A função de é igual a é frequentemente utilizada com o `condition` elemento para testar se um recurso está implementado.

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

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) verifica os diferentes tipos de valores de igualdade. Todos os valores padrão retornam True.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| checkInts | Booleano | Verdadeiro |
| checkStrings | Booleano | Verdadeiro |
| checkArrays | Booleano | Verdadeiro |
| checkObjects | Booleano | Verdadeiro |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) utiliza [não](resource-group-template-functions-logical.md#not) com **é igual a**.

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| checkNotEquals | Booleano | Verdadeiro |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>maior
`greater(arg1, arg2)`

Verifica se o primeiro valor é maior do que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou cadeia de caracteres |O primeiro valor para a comparação maior. |
| arg2 |Sim |int ou cadeia de caracteres |O segundo valor para a comparação maior. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se o primeiro valor é maior do que o segundo valor; caso contrário, **falso**.

### <a name="example"></a>Exemplo

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) verifica se um valor é maior do que o outro.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| checkInts | Booleano | Falso |
| checkStrings | Booleano | Verdadeiro |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior que ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou cadeia de caracteres |O primeiro valor para a comparação de maior ou igual. |
| arg2 |Sim |int ou cadeia de caracteres |O segundo valor para a comparação de maior ou igual. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se o primeiro valor é maior que ou igual ao segundo valor; caso contrário, **falso**.

### <a name="example"></a>Exemplo

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) verifica se um valor é maior que ou igual a outro.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| checkInts | Booleano | Falso |
| checkStrings | Booleano | Verdadeiro |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>menos
`less(arg1, arg2)`

Verifica se o primeiro valor é menor que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou cadeia de caracteres |O primeiro valor para a comparação menos. |
| arg2 |Sim |int ou cadeia de caracteres |O segundo valor para a comparação menos. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se o primeiro valor é menor que o segundo valor; caso contrário, **falso**.

### <a name="example"></a>Exemplo

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) verifica se um valor é menor do que o outro.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| checkInts | Booleano | Verdadeiro |
| checkStrings | Booleano | Falso |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é menor ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou cadeia de caracteres |O primeiro valor para o menor ou igual a comparação. |
| arg2 |Sim |int ou cadeia de caracteres |O segundo valor para o menor ou igual a comparação. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se o primeiro valor é menor que ou igual ao segundo valor; caso contrário, **falso**.

### <a name="example"></a>Exemplo

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) verifica se um valor é menor ou igual a outro.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| checkInts | Booleano | Verdadeiro |
| checkStrings | Booleano | Falso |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para intercalar vários modelos, veja [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

