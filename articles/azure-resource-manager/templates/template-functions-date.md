---
title: Funções do modelo - data
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com datas.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986281"
---
# <a name="date-functions-for-arm-templates"></a>Funções de data para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com datas nos seus modelos de Gestor de Recursos Azure (ARM):

* [dataTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dataTimeAdd

`dateTimeAdd(base, duration, [format])`

Adiciona uma duração de tempo ao valor da data-data base.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base | Sim | string | O valor da data de início para a adição. Utilize o formato de carimbo de [tempo ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sim | string | O valor do tempo para adicionar à base. Pode ser um valor negativo. Utilize o formato de [duração ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
| formato | Não | string | O formato de saída para o resultado da hora da data. Se não for fornecido, é utilizado o formato do valor base. Utilize cordas de [formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor devolvido

O valor da data que resulta da adição do valor de duração ao valor base.

### <a name="examples"></a>Exemplos

O modelo de exemplo seguinte mostra diferentes formas de adicionar valores de tempo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Quando o modelo anterior é implantado com `2020-04-07 14:53:14Z`um tempo base de, a saída é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| adicionar3Anos | String | 4/7/2023 14:53:14 |
| subtract9Days | String | 3/29/2020 14:53:14 |
| adicionar1Hora | String | 4/7/2020 15:53:14 |

O modelo de exemplo seguinte mostra como definir a hora de início para um horário de Automação.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Devolve o valor atual da data (UTC) no formato especificado. Se não for fornecido qualquer formato, é utilizado o formato ISO 8601 (yyyMMddTHHmmssZ). **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| formato |Não |string |O URI codificado valor para converter-se em uma corda. Utilize cordas de [formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor padrão de um parâmetro. A utilização desta função em qualquer outro lugar de um modelo devolve um erro. A função não é permitida noutras partes do modelo porque devolve um valor diferente cada vez que é chamada. A implantação do mesmo modelo com os mesmos parâmetros não produziria os mesmos resultados.

Se utilizar a [opção de reimplantar uma implementação mais precoce](rollback-on-error.md)e a implementação anterior inclui um parâmetro que utiliza utcNow, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado na implementação de reversão.

Tenha cuidado ao recolocar um modelo que dependa da função utcNow para um valor predefinido. Quando reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro a partir da implementação anterior.

### <a name="return-value"></a>Valor devolvido

O valor atual da data utc.

### <a name="examples"></a>Exemplos

O modelo de exemplo seguinte mostra diferentes formatos para o valor da data.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

O exemplo seguinte mostra como usar um valor a partir da função ao definir um valor de etiqueta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```