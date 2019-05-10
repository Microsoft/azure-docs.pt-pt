---
title: Formatar eventos com a pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Compreenda como formatar a eventos de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 38072d111d51cc2d2c6265643b69a870a679a454
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466148"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com a pré-visualização do Azure Time Series Insights

Este artigo ajuda a moldar o ficheiro JSON para maximizar a eficiência de suas consultas de pré-visualização do Azure Time Series Insights.

## <a name="best-practices"></a>Melhores práticas

É importante pensar sobre como enviar eventos para pré-visualização do Time Series Insights. Ou seja, deve sempre:

* envie dados através da rede mais eficientemente possível.
* Store seus dados de forma que o ajuda a agregá-la mais convenientemente para o seu cenário.

Para obter o melhor desempenho de consulta possível, efetue o seguinte:

* Não envie propriedades desnecessárias. Pré-visualização do Time Series Insights cobra-lhe na sua utilização. É melhor armazenar e processar os dados que irá consultar.
* Utilize os campos de instância para dados estáticos. Essa prática ajuda-o a evitar o envio de dados estáticos na rede. Campos de instância, um componente do modelo de série de tempo, funcionam como os dados no serviço de disponibilidade geral do Time Series Insights de referência. Para saber mais sobre os campos de instância, veja [modelos de série de tempo](./time-series-insights-update-tsm.md).
* Propriedades de dimensão entre dois ou mais eventos de partilha. Essa prática ajuda-o a enviar dados através da rede com mais eficiência.
* Não utilize o aninhamento de matriz profunda. Pré-visualização do Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. Pré-visualização do Time Series Insights nivela as matrizes de nas mensagens em vários eventos com pares de valor de propriedade.
* Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviar-lhe em separado reduz o número de eventos e, como menos eventos precisam ser processado, a prática pode a fazer consultas um melhor desempenho.

## <a name="example"></a>Exemplo

O exemplo seguinte baseia-se um cenário em que dois ou mais dispositivos enviam medições ou sinais. Foi possível a medidas ou sinais *taxa de fluxo*, *pressão do motor de petróleo*, *temperatura*, e *humidade*.

No exemplo a seguir, há uma única mensagem do IoT Hub, em que a matriz externa contém uma secção partilhada comuns de valores de dimensão. A matriz externa utiliza dados de instâncias de série de tempo para aumentar a eficiência da mensagem. Instância de série de tempo contém metadados de dispositivo, que não é alterado com cada evento, mas ela fornece propriedades úteis para análise de dados. Para guardar em bytes enviados durante a transmissão e tornar a mensagem mais eficiente, considere a criação de batches de valores de dimensão comuns e empregando metadados de instância de série de tempo.

### <a name="example-json-payload"></a>Payload JSON de exemplo

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Instância de série de tempo 
> [!NOTE]
> O ID de série de tempo é *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Pré-visualização do Time Series Insights une uma tabela (depois de mesclar) durante o tempo de consulta. A tabela inclui colunas adicionais, como **tipo**. O exemplo seguinte demonstra como é possível [forma](./time-series-insights-send-events.md#json) seus dados de telemetria:

| deviceId  | Tipo | L1 | ERROS DE L2 | carimbo de data/hora | série. Taxa de fluxo ft3/s | série. Psi de petróleo pressão do motor |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de bateria | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de bateria |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMUNS | SIMULADOR |    Sistema de bateria |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

No exemplo anterior, tenha em atenção os seguintes pontos:

* Propriedades estáticas são armazenadas no Time Series Insights pré-visualização para otimizar os dados enviados através da rede.
* Dados de pré-visualização de informações de série de tempo são associados no momento da consulta ao utilizar o *timeSeriesId* que é definido na instância.
* São utilizadas duas camadas de aninhamento, que é o máximo que é suportado pela pré-visualização do Time Series Insights. É fundamental para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, que são enviados como propriedades separadas dentro do mesmo objeto. No exemplo, **série. Fluxo de psi taxa**, **série. Psi de pressão de petróleo motor**, e **série. Fluxo de taxa ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Campos de instância não são armazenados com a telemetria. Eles são armazenados com metadados no **modelo de série de tempo**.
> A tabela anterior representa a visão de consulta.

## <a name="next-steps"></a>Passos Seguintes

- Para colocar estas diretrizes em prática, consulte [sintaxe de consulta de pré-visualização do Azure Time Series Insights](./time-series-insights-query-data-csharp.md). Ficará a saber que mais sobre a sintaxe de consulta para os dados de pré-visualização do Time Series Insights REST API de acesso.

- Para saber mais sobre formas JSON suportadas, veja [formas JSON suportadas](./time-series-insights-send-events.md#json).
