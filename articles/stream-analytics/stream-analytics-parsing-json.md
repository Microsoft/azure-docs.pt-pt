---
title: Analisando JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, dados formatados em CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: ac06521df38bdc91ca717d888c73cd541576014d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905445"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro. Os dados JSON e Avro podem ser estruturados e conter alguns tipos complexos, como objetos aninhados (registros) e matrizes. 

>[!NOTE]
>Os ficheiros AVRO criados pela Event Hub Capture utilizam um formato específico que requer que utilize a função de *desserializer personalizada.* Para mais informações, consulte [A entrada de ler em qualquer formato utilizando desserializers personalizados .NET](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



## <a name="record-data-types"></a>Tipos de dados de registro
Os tipos de dados de registro são usados para representar matrizes JSON e Avro quando os formatos correspondentes são usados nos fluxos de dados de entrada. Esses exemplos demonstram um sensor de exemplo, que está lendo eventos de entrada no formato JSON. Aqui está um exemplo de um único evento:

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

### <a name="access-nested-fields-in-known-schema"></a>Acessar campos aninhados no esquema conhecido
Use a notação de ponto (.) para acessar facilmente campos aninhados diretamente da sua consulta. Por exemplo, essa consulta seleciona as coordenadas de latitude e longitude sob a propriedade Location nos dados JSON anteriores. A notação de ponto pode ser usada para navegar por vários níveis, conforme mostrado abaixo.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

O resultado é:

|DispositivoID|Lat|Longo|Temperatura|Versão|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Selecionar todas as propriedades
Você pode selecionar todas as propriedades de um registro aninhado usando o curinga ' * '. Considere o seguinte exemplo:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

O resultado é:

|DispositivoID|Lat|Longo|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Acessar campos aninhados quando o nome da propriedade for uma variável

Use a função [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) se o nome da propriedade for uma variável. Isto permite construir consultas dinâmicas sem nomes de propriedade de codificação de dureza.

Por exemplo, imagine que o fluxo de dados da amostra precisa **de ser associado a dados** de referência que contenham limiares para cada sensor do dispositivo. Um trecho desses dados de referência é mostrado abaixo.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

O objetivo aqui é juntar o nosso conjunto de dados de amostra sintetizada desde o topo do artigo a esses dados de referência, e obter um evento para cada medida de sensor acima do seu limiar. Isto significa que o nosso único evento acima pode gerar múltiplos eventos de saída se vários sensores estiverem acima dos respetivos limiares, graças à adesão. Para obter resultados semelhantes sem uma adesão, consulte a secção abaixo.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**O GetRecordPropertyValue** seleciona a propriedade em *SensorReadings,* qual nome corresponde ao nome de propriedade proveniente dos dados de referência. Em seguida, o valor associado das *Leituras sensoriais* é extraído.

O resultado é:

|DispositivoID|Nome do Sensor|Mensagem de alerta|
|-|-|-|
|12345|Humidade|Alerta : Sensor acima do limiar|

### <a name="convert-record-fields-into-separate-events"></a>Converter campos de registro em eventos separados

Para converter campos de registro em eventos separados, use o operador [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função [getrecordproperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) .

Com os dados originais da amostra, a seguinte consulta poderia ser usada para extrair propriedades em diferentes eventos.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

O resultado é:

|DispositivoID|Nome do Sensor|Mensagem de alerta|
|-|-|-|
|12345|Temperatura|80|
|12345|Humidade|70|
|12345|Sensor personalizado01|5|
|12345|Sensor personalizado02|99|
|12345|SensorMetadata|[Objeto de objeto]|

Usando [COM,](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)é então possível encaminhar esses eventos para diferentes destinos:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

## <a name="array-data-types"></a>Tipos de dados de matriz

Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores de matriz são detalhadas abaixo. Esses exemplos usam as funções [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)e [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) Operator.

Aqui está um exemplo de um único evento. Tanto `CustomSensor03` como `SensorMetadata` são de **tipo matriz:**

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Trabalhando com um elemento de matriz específico

Selecione o elemento de matriz em um índice especificado (selecionando o primeiro elemento de matriz):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

O resultado é:

|primeiroElemento|
|-|
|12|

### <a name="select-array-length"></a>Selecionar comprimento da matriz

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

O resultado é:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Converter elementos de matriz em eventos separados

Selecione todos os elementos de matriz como eventos individuais. O operador [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) junto com a função interna [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extrai todos os elementos da matriz como eventos individuais:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

O resultado é:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
O resultado é:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Fabricante|ABC|
|12345|Versão|1.2.45|

Se os campos extraídos precisarem de aparecer nas colunas, é possível girar o conjunto de dados utilizando a sintaxe [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) para além da operação [JOIN.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) Essa adesão requer uma condição [de limite](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) temporal que impeça a duplicação:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

O resultado é:

|DeviceId|Lat|Longo|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Veja também
[Tipos de dados no Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
