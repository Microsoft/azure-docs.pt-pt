---
title: Funções de modelo – lógica
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: df8433d167a166fe94d965f81e42cd0b3e8f0e54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150688"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funções lógicas para modelos de Azure Resource Manager

O Gerenciador de recursos fornece várias funções para fazer comparações em seus modelos.

* [e](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [ou](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores de parâmetro são verdadeiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |boolean |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor de retorno

Retornará **true** se todos os valores forem verdadeiros; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

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
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

## <a name="bool"></a>booleano

`bool(arg1)`

Converte o parâmetro em um booliano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |Cadeia de caracteres ou int |O valor a ser convertido em booliano. |

### <a name="return-value"></a>Valor de retorno
Um booliano do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar bool com uma cadeia de caracteres ou um inteiro.

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

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| TrueString | Bool | Verdadeiro |
| falseString | Bool | Falso |
| trueInt | Bool | Verdadeiro |
| falseInt | Bool | Falso |

## <a name="if"></a>que

`if(condition, trueValue, falseValue)`

Retorna um valor com base em se uma condição é verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| condition |Sim |boolean |O valor para verificar se é verdadeiro ou falso. |
| trueValue |Sim | Cadeia de caracteres, int, objeto ou matriz |O valor a ser retornado quando a condição for verdadeira. |
| falseValue |Sim | Cadeia de caracteres, int, objeto ou matriz |O valor a ser retornado quando a condição for falsa. |

### <a name="return-value"></a>Valor de retorno

Retorna o segundo parâmetro quando o primeiro parâmetro é **true**; caso contrário, retorna o terceiro parâmetro.

### <a name="remarks"></a>Observações

Quando a condição for **verdadeira**, somente o valor verdadeiro será avaliado. Quando a condição for **falsa**, somente o valor false será avaliado. Com a função **If** , você pode incluir expressões que são apenas condicionalmente válidas. Por exemplo, você pode fazer referência a um recurso que existe sob uma condição, mas não sob a outra condição. Um exemplo de expressões de avaliação condicional é mostrado na seção a seguir.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como usar a função `if`.

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
| yesOutput | Cadeia | sim |
| noOutput | Cadeia | não |
| objectOutput | Objeto | {"Test": "value1"} |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar essa função com expressões que são apenas condicionalmente válidas.

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

## <a name="not"></a>válido

`not(arg1)`

Converte o valor booliano em seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O valor a ser convertido. |

### <a name="return-value"></a>Valor de retorno

Retorna **true** quando o parâmetro é **false**. Retorna **false** quando o parâmetro é **true**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

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
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa **não** com [Equals](resource-group-template-functions-comparison.md#equals).

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
| checkNotEquals | Bool | Verdadeiro |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se algum valor de parâmetro é true.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |boolean |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor de retorno

Retornará **true** se qualquer valor for true; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

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
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | Verdadeiro |
| notExampleOutput | Bool | Falso |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para intercalar vários modelos, veja [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

