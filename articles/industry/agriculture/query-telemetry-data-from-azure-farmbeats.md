---
title: Consultar dados telemétricos ingeridos
description: Este artigo descreve como consultar dados de telemetria ingeridos.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80349811"
---
# <a name="query-ingested-telemetry-data"></a>Consultar dados telemétricos ingeridos

Este artigo descreve como consultar dados de sensores ingeridos da Azure FarmBeats.

Ingerir dados da Internet das Coisas (IoT) recursos como dispositivos e sensores é um cenário comum no FarmBeats. Cria metadados para dispositivos e sensores e, em seguida, ingere os dados históricos ao FarmBeats num formato canónico. Uma vez que os dados do sensor estão disponíveis no Dadohub FarmBeats, podemos consultar o mesmo para gerar insights accuíveis ou construir modelos.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, certifique-se de que instalou FarmBeats e ingeriu dados de telemetria de sensores dos seus dispositivos IoT para FarmBeats.

Para ingerir dados de telemetria sensor, visite [dados históricos de telemetria](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Antes de prosseguir, também precisa de se certificar de que está familiarizado com as APIs de REST farmbeats, uma vez que irá consultar a telemetria ingerida utilizando as APIs. Para obter mais informações sobre As APIs do FarmBeats, consulte [FarmBeats REST APIs](rest-api-in-azure-farmbeats.md). **Certifique-se de que é capaz de fazer pedidos de API para o seu ponto final FarmBeats Datahub**.

## <a name="query-ingested-sensor-telemetry-data"></a>Dados de telemetria de sensores ingeridos

Existem duas formas de aceder e consultar dados de telemetria da FarmBeats:

- API e
- Insights de séries temporítros (TSI).

### <a name="query-using-rest-api"></a>Consulta usando REST API

Siga os passos para consultar os dados de telemetria do sensor ingeridos utilizando AS APIs do FarmBeats REST:

1. Identifique o sensor em que está interessado. Pode fazê-lo fazendo um pedido GET em /Sensor API.

> [!NOTE]
> O **id** e o **sensorModelId** do objeto sensor interessado.

2. Faça um GET/{id} em /SensorModel API para o **sensorModelId** como indicado no passo 1. O "Sensor Model" tem todos os metadados e detalhes sobre a telemetria ingerida do sensor. Por exemplo, **a Medida sensorial** dentro do objeto **Sensor Model** tem detalhes sobre que medidas é o envio do sensor e em que tipos e unidades. Por exemplo,

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Tome nota da resposta da chamada GET/{id} para o Modelo de Sensor.

3. Faça uma chamada POST em /Telemetria API com a seguinte carga útil de entrada

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. A resposta da API /Telemetria será algo assim:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
Na resposta do exemplo acima, a telemetria do sensor consultada dá dados para dois timetamps juntamente com o nome da medida ("moist_soil_last") e valores da telemetria relatada nos doismps. Terá de consultar o Modelo de Sensor associado (conforme descrito no passo 2) para interpretar o tipo e a unidade dos valores relatados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta utilizando insights da série de tempo Azure (TSI)

FarmBeats aproveita [a Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e visualizar dados em escala IoT-dados altamente contextualizados e otimizados para séries temporizadas.

Os dados de telemetria são recebidos num EventHub e depois processados e empurrados para um ambiente TSI dentro do grupo de recursos FarmBeats. Os dados podem então ser consultados diretamente a partir da EES. Para mais informações, consulte [a documentação da TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Siga os passos para visualizar os dados sobre a EDIÇÃO:

1. Vá ao grupo de recursos **Azure Portal**  >  **FarmBeats DataHub** > selecione **Time Series Insights** ambiente (tsi-xxxx) > Políticas de Acesso a **Dados**. Adicione o utilizador com acesso ao Leitor ou ao Contribuinte.
2. Aceda à página **geral** do ambiente **Time Series Insights** (tsi-xxxx) e selecione o URL do Time Series Insights **Explorer**. Agora poderá visualizar a telemetria ingerida.

Além de armazenar, consultar e visualizar a telemetria, a TSI também permite a integração num dashboard Power BI. Para mais informações, consulte [aqui]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Passos seguintes

Agora, já questionou os dados dos sensores da sua instância Azure FarmBeats. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
