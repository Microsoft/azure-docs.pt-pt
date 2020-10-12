---
title: Funções do modelo - lógica
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8fe1c00240fc24c3c1454b118f9e0d9a9d54fe4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677394"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos Azure Resource Manager (ARM).

* [and](#and)
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

Devoluções **Verdadeiras** se todos os valores forem verdadeiros; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro num booleano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |corda ou int |O valor para converter para um boolean. |

### <a name="return-value"></a>Valor devolvido
Um booleano do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar bool com uma corda ou inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| falseString | Booleano | Falso |
| trueInt | Booleano | Verdadeiro |
| falseInt | Booleano | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Devolve um valor baseado no facto de uma condição ser verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| condição |Sim |boolean |O valor para verificar se é verdade ou falso. |
| verdadeiroValue |Sim | cadeia, int, objeto ou matriz |O valor para voltar quando a condição é verdadeira. |
| falsoValue |Sim | cadeia, int, objeto ou matriz |O valor para devolver quando a condição é falsa. |

### <a name="return-value"></a>Valor devolvido

Devolve o segundo parâmetro quando o primeiro parâmetro é **verdadeiro;** caso contrário, devolve o terceiro parâmetro.

### <a name="remarks"></a>Observações

Quando a condição é **verdadeira,** apenas o verdadeiro valor é avaliado. Quando a condição é **falsa,** apenas o valor falso é avaliado. Com a função **se,** pode incluir expressões que são apenas válidas condicionalmente. Por exemplo, pode referenciar um recurso que existe sob uma condição, mas não sob outra condição. Na secção seguinte é apresentado um exemplo de avaliação condicional das expressões.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como utilizar a `if` função.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| objetoOutput | Objeto | { "teste": "valor1" } |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar esta função com expressões que são apenas válidas condicionalmente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Converte o valor booleano ao seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |boolean |O valor para converter. |

### <a name="return-value"></a>Valor devolvido

**Retorna verdadeiro** quando o parâmetro é **falso**. Retorna **falso** quando o parâmetro é **verdadeiro.**

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir **não** usa com [iguais](template-functions-comparison.md#equals).

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

Devoluções **Verdadeiras** se algum valor for verdadeiro; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).

