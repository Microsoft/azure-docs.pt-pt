---
title: Funções do Azure Resource Manager modelo - lógicas | Documentos da Microsoft
description: Descreve as funções para utilizar num modelo do Azure Resource Manager para determinar os valores lógicos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: 4684c38fe506ed912c6827f1e60b94b847024347
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405656"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funções lógicas para modelos Azure Resource Manager

Resource Manager fornece várias funções para fazer comparações nos seus modelos.

* [and](#and)
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
| arg2 |Sim |boolean |O segundo valor a verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiras. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se todos os valores são verdadeiro; caso contrário, **falso**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) mostra como utilizar funções lógicas.

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="bool"></a>booleano

`bool(arg1)`

Converte o parâmetro para um valor booleano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou int |O valor a converter em booleano. |

### <a name="return-value"></a>Valor de retorno
Um valor booleano do valor convertido.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) mostra como utilizar bool com uma cadeia de caracteres ou um número inteiro.

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

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| trueString | Booleano | Verdadeiro |
| falseString | Booleano | Falso |
| trueInt | Booleano | Verdadeiro |
| falseInt | Booleano | Falso |

## <a name="if"></a>IF

`if(condition, trueValue, falseValue)`

Devolve um valor com base em se uma condição for true ou false.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| condition |Sim |boolean |O valor a verificar se ela é true ou false. |
| trueValue |Sim | cadeia de caracteres, int, objeto ou matriz |O valor a devolver quando a condição for verdadeira. |
| falseValue |Sim | cadeia de caracteres, int, objeto ou matriz |O valor a devolver quando a condição for falsa. |

### <a name="return-value"></a>Valor de retorno

Devolve o segundo parâmetro quando o primeiro parâmetro é **True**; caso contrário, devolve o terceiro parâmetro.

### <a name="remarks"></a>Observações

Quando a condição for **True**, é avaliado apenas o valor true. Quando a condição for **False**, é avaliado apenas o valor false. Com o **se** função, pode incluir expressões que são apenas condicionalmente válidas. Por exemplo, pode fazer referência a um recurso que existe numa condição, mas não está sob a outra condição. Um exemplo de condicionalmente avaliação de expressões é mostrado na secção seguinte.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) mostra como utilizar o `if` função.

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| yesOutput | String | sim |
| noOutput | String | não |
| objectOutput | Object | { "test": "value1" } |

O seguinte procedimento [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) mostra como utilizar esta função com expressões que são apenas condicionalmente válidas.

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

## <a name="not"></a>não

`not(arg1)`

Converte o valor booleano para seu valor oposta.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O valor a converter. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** quando o parâmetro é **falso**. Devolve **False** quando o parâmetro é **verdadeiro**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) mostra como utilizar funções lógicas.

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) utiliza **não** com [é igual a](resource-group-template-functions-comparison.md#equals).

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| checkNotEquals | Booleano | Verdadeiro |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se a qualquer valor de parâmetro é verdadeira.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |boolean |O segundo valor a verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiras. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se qualquer valor for verdadeiro; caso contrário, **falso**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) mostra como utilizar funções lógicas.

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

O resultado do exemplo anterior é:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| andExampleOutput | Booleano | Falso |
| orExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para intercalar vários modelos, veja [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

