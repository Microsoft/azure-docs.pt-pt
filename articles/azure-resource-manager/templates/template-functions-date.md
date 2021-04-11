---
title: Funções do modelo - data
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para trabalhar com datas.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: abff5b86ad1e10042596b11f613cdb594e307209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889931"
---
# <a name="date-functions-for-arm-templates"></a>Funções de data para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com datas no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [dataTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dataTimeAdd

`dateTimeAdd(base, duration, [format])`

Adiciona uma duração de tempo a um valor base. Espera-se o formato ISO 8601.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| base | Yes | string | O valor da data inicial para a adição. Utilize [o formato iso 8601 timetamp](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Yes | string | O valor do tempo a adicionar à base. Pode ser um valor negativo. Utilize [o formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formato | No | string | O formato de saída para o resultado da hora da data. Se não for fornecido, o formato do valor base é utilizado. Utilize [cordas de formato padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor devolvido

O valor da data que resulta da adição do valor da duração ao valor base.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes formas de adicionar valores de tempo.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
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
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Quando o modelo anterior é implantado com um tempo de base `2020-04-07 14:53:14Z` de, a saída é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 14:53:14 |
| subtrair9DaysOutput | String | 3/29/2020 14:53:14 |
| add1HourOutput | String | 4/7/2020 15:53:14 |

O modelo de exemplo seguinte mostra como definir a hora de início para um horário de automação.

# <a name="json"></a>[JSON](#tab/json)

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
    "baseTime": {
      "type": "string",
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
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Devolve o valor atual (UTC) da data no formato especificado. Se não for fornecido qualquer formato, o formato ISO 8601 ( `yyyyMMddTHHmmssZ` ) é utilizado. **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| formato |No |string |O valor codificado uri para converter-se a uma corda. Utilize [cordas de formato padrão](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cordas de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor predefinido de um parâmetro. A utilização desta função em qualquer outro lugar de um modelo retorna um erro. A função não é permitida noutras partes do modelo porque devolve um valor diferente cada vez que é chamado. A implantação do mesmo modelo com os mesmos parâmetros não produziria de forma fiável os mesmos resultados.

Se utilizar a [opção de reversão do erro](rollback-on-error.md) para uma implementação com sucesso anterior, e a implementação anterior inclui um parâmetro que utiliza utcNow, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado na implementação de reversão.

Tenha cuidado ao recolocar um modelo que dependa da função utcNow para um valor predefinido. Quando se recoloca e não fornece um valor para o parâmetro, a função é reavaliada. Se pretender atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro a partir da implementação anterior.

### <a name="return-value"></a>Valor devolvido

O valor atual da data utc.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes formatos para o valor da hora da data.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

O exemplo seguinte mostra como utilizar um valor da função ao definir um valor de etiqueta.

# <a name="json"></a>[JSON](#tab/json)

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
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "westeurope",
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
