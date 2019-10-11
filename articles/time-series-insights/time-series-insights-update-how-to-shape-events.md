---
title: Eventos de forma com Azure Time Series Insights visualização | Microsoft Docs
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
ms.openlocfilehash: ed0594373c8702ab01b50facaf0ef5ece2d6c7e1
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274265"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com Azure Time Series Insights visualização

Este artigo ajuda você a formatar seu arquivo JSON para maximizar a eficiência de suas Azure Time Series Insights Visualizar consultas.

## <a name="best-practices"></a>Melhores práticas

Pense em como você envia eventos para Time Series Insights visualização. Ou seja, você deve sempre:

* Envie dados pela rede o mais eficiente possível.
* Armazene seus dados de uma maneira que o ajude a agregar mais adequadamente para seu cenário.

Para obter o melhor desempenho de consulta possível, faça o seguinte:

* Não envie Propriedades desnecessárias. Time Series Insights visualização cobra sobre seu uso. É melhor armazenar e processar os dados que você consultará.
* Use campos de instância para dados estáticos. Essa prática ajuda você a evitar o envio de dados estáticos pela rede. Os campos de instância, um componente do modelo de série temporal, funcionam como dados de referência na Time Series Insights serviço geralmente disponível. Para saber mais sobre os campos de instância, consulte [modelos de série temporal](./time-series-insights-update-tsm.md).
* Compartilhe Propriedades de dimensão entre dois ou mais eventos. Essa prática ajuda você a enviar dados pela rede com mais eficiência.
* Não use o aninhamento profundo de matriz. Time Series Insights visualização dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. Time Series Insights visualização achata as matrizes nas mensagens em vários eventos com pares de valor de propriedade.
* Se existirem apenas algumas medidas para todos ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

## <a name="example"></a>Exemplo

O exemplo a seguir se baseia em um cenário em que dois ou mais dispositivos enviam medidas ou sinais. As medidas ou os sinais podem ser *taxa de fluxo*, pressão de óleo do *motor*, *temperatura*e *umidade*.

No exemplo a seguir, há uma única mensagem do Hub IoT do Azure em que a matriz externa contém uma seção compartilhada de valores de dimensão comuns. A matriz externa usa dados de instância de série temporal para aumentar a eficiência da mensagem. A instância de série temporal contém metadados de dispositivo, que não são alterados com todos os eventos, mas fornece propriedades úteis para análise de dados. Para salvar em bytes enviados pela transmissão e tornar a mensagem mais eficiente, considere o envio em lote de valores de dimensão comuns e o emprego de metadados de instância de série temporal.

### <a name="example-json-payload"></a>Exemplo de carga JSON

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

### <a name="time-series-instance"></a>Instância de série temporal 
> [!NOTE]
> A ID da série temporal é *DeviceID*.

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

Time Series Insights visualização une uma tabela (após o nivelamento) durante o tempo de consulta. A tabela inclui colunas adicionais, como **tipo**. O exemplo a seguir demonstra como você pode [Formatar](./time-series-insights-send-events.md#supported-json-shapes) seus dados de telemetria.

| deviceId  | Tipo | L1 | Cache | carimbo de data/hora | série. Taxa de fluxo ft3/s | série. Pressão do óleo do motor psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULA | Sistema de bateria | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULA |   Sistema de bateria |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA COMUM | SIMULA |    Sistema de bateria |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

No exemplo anterior, observe os seguintes pontos:

* As propriedades estáticas são armazenadas na visualização Time Series Insights para otimizar os dados enviados pela rede.
* Time Series Insights dados de visualização são Unidos no momento da consulta usando a ID da série temporal definida na instância.
* São usadas duas camadas de aninhamento, que é a mais compatível com Time Series Insights versão prévia. É fundamental evitar matrizes profundamente aninhadas.
* Como há poucas medidas, elas são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **série. Taxa de fluxo psi**, **série. Séries psi**e série do óleo do motor **. A taxa de fluxo ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Os campos de instância não são armazenados com telemetria. Eles são armazenados com metadados no **modelo de série temporal**.
> A tabela anterior representa a exibição de consulta.

## <a name="next-steps"></a>Passos seguintes

- Para colocar essas diretrizes em prática, consulte [Azure Time Series insights sintaxe de consulta de visualização](./time-series-insights-query-data-csharp.md). Você aprenderá mais sobre a sintaxe de consulta para a API REST de acesso a dados do Time Series Insights Preview.
- Para saber mais sobre as formas JSON com suporte, consulte [formas de JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).
