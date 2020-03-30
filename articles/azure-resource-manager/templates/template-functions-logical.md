---
title: Funções do modelo - lógica
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156315"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos de Gestor de Recursos Azure (ARM).

* [e](#and)
* [bool](#bool)
* [se](#if)
* [não](#not)
* [ou](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores dos parâmetros são verdadeiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O primeiro valor para verificar se é verdade. |
| arg2 |Sim |boolean |O segundo valor para verificar se é verdade. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se todos os valores forem verdadeiros; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) de exemplo seguinte mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro para um booleano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |corda ou int |O valor para converter-se em boolean. |

### <a name="return-value"></a>Valor devolvido
Uma booleana do valor convertido.

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) de exemplo mostra como usar bool com uma corda ou um inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| trueString | Booleano | Verdadeiro |
| falsoString | Booleano | Falso |
| trueInt | Booleano | Verdadeiro |
| falsoInt | Booleano | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Devolve um valor baseado em se uma condição é verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| condição |Sim |boolean |O valor para verificar se é verdade ou falso. |
| trueValue |Sim | corda, int, objeto ou matriz |O valor para devolver quando a condição é verdadeira. |
| falsoValor |Sim | corda, int, objeto ou matriz |O valor a devolver quando a condição é falsa. |

### <a name="return-value"></a>Valor devolvido

Devolve o segundo parâmetro quando o primeiro parâmetro for **verdadeiro;** caso contrário, devolve o terceiro parâmetro.

### <a name="remarks"></a>Observações

Quando a condição é **Verdadeira,** apenas o verdadeiro valor é avaliado. Quando a condição é **Falsa,** apenas o valor falso é avaliado. Com a função **se,** pode incluir expressões que são apenas válidas condicionalmente. Por exemplo, pode fazer referência a um recurso que existe sob uma condição, mas não sob outra condição. Um exemplo de avaliação condicional das expressões é mostrado na secção seguinte.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) de exemplo seguinte `if` mostra como usar a função.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| simOutput | Cadeia | sim |
| noOutput | Cadeia | não |
| objetoSaída | Objeto | { "teste": "value1" } |

O [seguinte modelo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) de exemplo mostra como usar esta função com expressões que são apenas condicionalmente válidas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Converte o valor booleano para o seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O valor para converter. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** quando o parâmetro é **falso**. Devoluções **Falsas** quando o parâmetro é **verdadeiro**.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) de exemplo seguinte mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) de exemplo seguinte **não** utiliza com [iguais](template-functions-comparison.md#equals).

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
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Booleano | Verdadeiro |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se qualquer valor de parâmetro é verdadeiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O primeiro valor para verificar se é verdade. |
| arg2 |Sim |boolean |O segundo valor para verificar se é verdade. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se algum valor for verdadeiro; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) de exemplo seguinte mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .

