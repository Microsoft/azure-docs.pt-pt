---
title: Análise de JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, CSV formatado dados.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329362"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

O Azure Stream Analytics suporta o processamento de eventos em formatos de dados do CSV, JSON e Avro. Dados JSON e Avro podem ser estruturados e contêm alguns tipos complexos, como objetos aninhados (registos) e matrizes. 




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
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Campos de acesso aninhado no esquema conhecido
Utilize a notação de ponto (.) para aceder facilmente aos campos aninhados diretamente a partir de sua consulta. Por exemplo, esta consulta seleciona as coordenadas de Latitude e Longitude sob a propriedade de localização dos dados JSON anterior. A notação de ponto pode ser utilizada para navegar vários níveis, conforme mostrado abaixo.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Selecione todas as propriedades
Pode selecionar todas as propriedades de um registo aninhado, usando ' *' carateres universais. Considere o exemplo a seguir:

```SQL
SELECT input.Location.*
FROM input
```

O resultado é:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Campos aninhado de acesso quando o nome da propriedade é uma variável
Utilize o [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) funcionam se o nome da propriedade é uma variável. 

Por exemplo, imagine que um fluxo de dados de exemplo tem de ser associado com limiares de contenção de dados de referência de sensor cada dispositivo. Um trecho de tais dados de referência é mostrado abaixo.

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
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Converter os campos de registo em eventos separados
Para converter os campos de registo eventos separados, utilize o [aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operador em conjunto com o [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) função. Por exemplo, se o exemplo anterior tinha vários registos para SensorReading, a seguinte consulta poderia ser usada para extrai-los para eventos diferentes:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tipos de dados de matriz

Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores de matriz são detalhadas abaixo. Estes exemplos partem do princípio de eventos de entrada tem uma propriedade chamada "arrayField" Isto é um tipo de dados de matriz.

Estes exemplos utilizam as funções [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)e a [aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operador.

### <a name="working-with-a-specific-array-element"></a>Trabalhar com um elemento de matriz específico
Selecione o elemento de matriz de um índice especificado (selecionando o primeiro elemento de matriz):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Selecione o comprimento da matriz

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Converter os elementos de matriz em eventos separados
Selecione todos os elemento de matriz como eventos individuais. O [aplicar](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operador em conjunto com o [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) função incorporada extrai todos os elementos de matriz como eventos individuais:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Consultar Também
[Tipos de dados no Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
