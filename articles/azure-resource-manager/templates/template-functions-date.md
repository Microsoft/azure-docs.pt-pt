---
title: Funções do modelo - data
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com datas.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058049"
---
# <a name="date-functions-for-arm-templates"></a>Funções de data para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com datas nos seus modelos Azure Resource Manager (ARM):

* [dataTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dataTimeAdd

`dateTimeAdd(base, duration, [format])`

Adiciona uma duração de tempo a um valor base. Espera-se o formato ISO 8601.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base | Sim | string | O valor da data inicial para a adição. Utilize [o formato iso 8601 timetamp](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sim | string | O valor do tempo a adicionar à base. Pode ser um valor negativo. Utilize [o formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formato | Não | string | O formato de saída para o resultado da hora da data. Se não for fornecido, o formato do valor base é utilizado. Utilize [cordas de formato padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor devolvido

O valor da data que resulta da adição do valor da duração ao valor base.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes formas de adicionar valores de tempo.

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

Quando o modelo anterior é implantado com um tempo de base `2020-04-07 14:53:14Z` de, a saída é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| add3Years | Cadeia | 4/7/2023 14:53:14 |
| subtrair9Days | Cadeia | 3/29/2020 14:53:14 |
| add1Hour | Cadeia | 4/7/2020 15:53:14 |

O modelo de exemplo seguinte mostra como definir a hora de início para um horário de automação.

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

Devolve o valor atual (UTC) da data no formato especificado. Se não for fornecido nenhum formato, o formato ISO 8601 (yyyMddTHHmmssZ) é utilizado. **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| formato |Não |string |O valor codificado uri para converter-se a uma corda. Utilize [cordas de formato padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor predefinido de um parâmetro. A utilização desta função em qualquer outro lugar de um modelo retorna um erro. A função não é permitida noutras partes do modelo porque devolve um valor diferente cada vez que é chamado. A implantação do mesmo modelo com os mesmos parâmetros não produziria de forma fiável os mesmos resultados.

Se utilizar a [opção de reversão do erro](rollback-on-error.md) para uma implementação com sucesso anterior, e a implementação anterior inclui um parâmetro que utiliza utcNow, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado na implementação de reversão.

Tenha cuidado ao recolocar um modelo que dependa da função utcNow para um valor predefinido. Quando se recoloca e não fornece um valor para o parâmetro, a função é reavaliada. Se pretender atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro a partir da implementação anterior.

### <a name="return-value"></a>Valor devolvido

O valor atual da data utc.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes formatos para o valor da hora da data.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

O exemplo seguinte mostra como utilizar um valor da função ao definir um valor de etiqueta.

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

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo de Gestor de Recursos Azure, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
