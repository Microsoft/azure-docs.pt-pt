---
title: Formatar eventos com a pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Entenda como formatar eventos com Azure Time Series Insights visualização.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e1eb0d7450e1a7f263f29b8d4657547dd85d4276
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883299"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com a pré-visualização do Azure Time Series Insights

Este artigo ajuda a moldar o ficheiro JSON para maximizar a eficiência de suas consultas de pré-visualização do Azure Time Series Insights.

## <a name="best-practices"></a>Melhores práticas

Pense em como você envia eventos para Time Series Insights visualização. Ou seja, deve sempre:

* envie dados através da rede mais eficientemente possível.
* Store seus dados de forma que o ajuda a agregá-la mais convenientemente para o seu cenário.

Para obter o melhor desempenho de consulta possível, efetue o seguinte:

* Não envie propriedades desnecessárias. Pré-visualização do Time Series Insights cobra-lhe na sua utilização. É melhor armazenar e processar os dados que irá consultar.
* Utilize os campos de instância para dados estáticos. Essa prática ajuda-o a evitar o envio de dados estáticos na rede. Os campos de instância, um componente do modelo de série temporal, funcionam como dados de referência na Time Series Insights serviço geralmente disponível. Para saber mais sobre os campos de instância, veja [modelos de série de tempo](./time-series-insights-update-tsm.md).
* Propriedades de dimensão entre dois ou mais eventos de partilha. Essa prática ajuda-o a enviar dados através da rede com mais eficiência.
* Não utilize o aninhamento de matriz profunda. Time Series Insights visualização dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. Pré-visualização do Time Series Insights nivela as matrizes de nas mensagens em vários eventos com pares de valor de propriedade.
* Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

## <a name="example"></a>Exemplo

O exemplo seguinte baseia-se um cenário em que dois ou mais dispositivos enviam medições ou sinais. As medidas ou os sinais podem ser *taxa de fluxo*, pressão de óleo do *motor*, *temperatura*e *umidade*.

No exemplo a seguir, há uma única mensagem do Hub IoT do Azure em que a matriz externa contém uma seção compartilhada de valores de dimensão comuns. A matriz externa utiliza dados de instâncias de série de tempo para aumentar a eficiência da mensagem. Instância de série de tempo contém metadados de dispositivo, que não é alterado com cada evento, mas ela fornece propriedades úteis para análise de dados. Para guardar em bytes enviados durante a transmissão e tornar a mensagem mais eficiente, considere a criação de batches de valores de dimensão comuns e empregando metadados de instância de série de tempo.

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

Pré-visualização do Time Series Insights une uma tabela (depois de mesclar) durante o tempo de consulta. A tabela inclui colunas adicionais, como **tipo**. O exemplo a seguir demonstra como você pode [Formatar](./time-series-insights-send-events.md#json) seus dados de telemetria.

| deviceId  | Tipo | L1 | ERROS DE L2 | carimbo de data/hora | série. Taxa de fluxo ft3/s | série. Psi de petróleo pressão do motor |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de bateria | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de bateria |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMUNS | SIMULADOR |    Sistema de bateria |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

No exemplo anterior, tenha em atenção os seguintes pontos:

* Propriedades estáticas são armazenadas no Time Series Insights pré-visualização para otimizar os dados enviados através da rede.
* Time Series Insights dados de visualização são Unidos no momento da consulta usando a ID da série temporal definida na instância.
* São utilizadas duas camadas de aninhamento, que é o máximo que é suportado pela pré-visualização do Time Series Insights. É fundamental para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, que são enviados como propriedades separadas dentro do mesmo objeto. No exemplo, **série. Fluxo de psi taxa**, **série. Psi de pressão de petróleo motor**, e **série. Fluxo de taxa ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Os campos de instância não são armazenados com telemetria. Eles são armazenados com metadados no **modelo de série temporal**.
> A tabela anterior representa a visão de consulta.

## <a name="next-steps"></a>Passos Seguintes

- Para colocar estas diretrizes em prática, consulte [sintaxe de consulta de pré-visualização do Azure Time Series Insights](./time-series-insights-query-data-csharp.md). Ficará a saber que mais sobre a sintaxe de consulta para os dados de pré-visualização do Time Series Insights REST API de acesso.
- Para saber mais sobre formas JSON suportadas, veja [formas JSON suportadas](./time-series-insights-send-events.md#json).
