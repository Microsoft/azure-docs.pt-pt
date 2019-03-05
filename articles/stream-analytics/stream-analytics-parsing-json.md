---
title: Análise de JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, CSV formatado dados.
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341426"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

O Azure Stream Analytics suporta o processamento de eventos em formatos de dados do CSV, JSON e Avro. Dados JSON e Avro podem conter tipos complexos, como objetos aninhados (registos) e matrizes.

## <a name="array-data-types"></a>Tipos de dados de matriz
Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores de matriz são detalhadas abaixo. Estes exemplos partem do princípio de eventos de entrada tem uma propriedade chamada "arrayField" Isto é um tipo de dados de matriz.

Estes exemplos utilizam as funções [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics)e a [aplicar](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operador.

## <a name="examples"></a>Exemplos
Selecione o elemento de matriz de um índice especificado (selecionando o primeiro elemento de matriz):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

Selecione o comprimento da matriz:

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

Selecione todos os elemento de matriz como eventos individuais. O [aplicar](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operador em conjunto com o [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) função incorporada extrai todos os elementos de matriz como eventos individuais:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```

## <a name="record-data-types"></a>Tipos de dados de registo
Tipos de dados de registo são utilizados para representar matrizes JSON e Avro quando formatos correspondentes são utilizados em fluxos de dados de entrada. Estes exemplos demonstram um sensor de exemplo, o que está a ler eventos de entrada no formato JSON. Eis o exemplo de um único evento:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99
    }
}
```

## <a name="examples"></a>Exemplos
Utilize a notação de ponto (.) para aceder a campos aninhados. Por exemplo, esta consulta seleciona o Lat e há muito tempo coordena sob a propriedade de localização dos dados JSON anterior:

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long
FROM input
```

Utilize o [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) funcionam se o nome da propriedade é desconhecido. Por exemplo, imagine que um fluxo de dados de exemplo tem de ser associado com limiares de contenção de dados de referência de sensor cada dispositivo:

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input
JOIN thresholds
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

Para converter os campos de registo eventos separados, utilize o [aplicar](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operador em conjunto com o [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) função. Por exemplo, para converter um fluxo de exemplo num fluxo de eventos com as leituras dos sensores individuais, esta consulta poderia ser usada:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Pode selecionar todas as propriedades de um registo aninhado, usando ' *' carateres universais. Considere o exemplo a seguir:

```SQL
SELECT input.SensorReadings.*
FROM input
```

O resultado é:

```json
{
    "Temperature" : 80,
    "Humidity" : 70,
    "CustomSensor01" : 5,
    "CustomSensor022" : 99
}
```

## <a name="see-also"></a>Consultar Também
[Tipos de dados no Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
