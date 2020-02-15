---
title: Funções do modelo - numérico
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com números.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 91aa637701acb278e81b7eb86aa3ae2db15acc28
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207254"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funções numéricas para modelos de Gestor de Recursos Azure

O Gestor de Recursos fornece as seguintes funções para trabalhar com os inteiros:

* [adicionar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [flutuar](#float)
* [int](#int)
* [máximo](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>adicionar
`add(operand1, operand2)`

Devolve a soma dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- | 
|operand1 |Sim |int |Primeiro número a adicionar. |
|operand2 |Sim |int |Segundo número a acrescentar. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) de exemplo seguinte adiciona dois parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Devolve o índice de um ciclo de iteração. 

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| loopName | Não | string | O nome do loop para obter a iteração. |
| compensado |Não |int |O número a adicionar ao valor de iteração baseado em zero. |

### <a name="remarks"></a>Observações

Esta função é sempre utilizada com um objeto **de cópia.** Se não for previsto qualquer valor para **compensação,** o valor atual da iteração é devolvido. O valor da iteração começa a zero. Pode utilizar loops de iteração ao definir recursos ou variáveis.

A propriedade **loopName** permite especificar se o copyIndex está se referindo a uma iteração de recursos ou iteração de propriedade. Se não for previsto qualquer valor para **o loopName,** a iteração do tipo de recurso atual é utilizada. Forneça um valor para **loopName** quando iterar uma propriedade. 
 
Para obter uma descrição completa de como utiliza o **copyIndex,** consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Do Azure](copy-resources.md).

Para um exemplo de utilização do **copyIndex** ao definir uma variável, consulte [Variáveis](template-syntax.md#variables).

### <a name="example"></a>Exemplo

O exemplo seguinte mostra um ciclo de cópia e o valor do índice incluído no nome. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o índice atual da iteração.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Devolve a divisão inteiro dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número está a ser dividido. |
| operand2 |Sim |int |O número que é usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor de retorno

Um inteiro a representar a divisão.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) de exemplo seguinte divide um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Converte o valor para um número de ponto flutuante. Só utiliza esta função ao passar parâmetros personalizados para uma aplicação, como uma Aplicação Lógica.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |corda ou int |O valor para converter para um número de ponto flutuante. |

### <a name="return-value"></a>Valor de retorno
Um número de ponto flutuante.

### <a name="example"></a>Exemplo

O exemplo que se segue mostra como usar o flutuador para passar parâmetros para uma Aplicação Lógica:

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

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Converte o valor especificado para um inteiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |corda ou int |O valor para converter-se a um inteiro. |

### <a name="return-value"></a>Valor de retorno

Um inteiro do valor convertido.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) de exemplo seguinte converte o valor do parâmetro fornecido pelo utilizador para inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>máx
`max (arg1)`

Devolve o valor máximo de um conjunto de inteiros ou de uma lista separada de inteiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |conjunto de inteiros, ou lista separada de inteiros |A coleção para obter o valor máximo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) de exemplo mostra como usar o máximo com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>mín
`min (arg1)`

Devolve o valor mínimo de um conjunto de inteiros ou de uma lista separada de inteiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |conjunto de inteiros, ou lista separada de inteiros |A coleção para obter o valor mínimo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) de exemplo mostra como usar min com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Devolve o restante da divisão inteiro usando os dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número está a ser dividido. |
| operand2 |Sim |int |O número que é usado para dividir, não pode ser 0. |

### <a name="return-value"></a>Valor de retorno
Um inteiro representando o restante.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) de exemplo seguinte devolve o restante de dividir um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Devolve a multiplicação dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |Primeiro número a multiplicar- se. |
| operand2 |Sim |int |Segundo número a multiplicar. |

### <a name="return-value"></a>Valor de retorno

Um inteiro representando a multiplicação.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) de exemplo seguinte multiplica um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Devolve a subtração dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número que é subtraído de. |
| operand2 |Sim |int |O número que é subtraído. |

### <a name="return-value"></a>Valor de retorno
Um inteiro representando a subtração.

### <a name="example"></a>Exemplo

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) de exemplo seguinte subtrai um parâmetro de outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| subResultado | Int | 4 |

Para implementar este modelo de exemplo com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Para implementar este modelo de exemplo com o PowerShell, utilize:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Passos seguintes
* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .

