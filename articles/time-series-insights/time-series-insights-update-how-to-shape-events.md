---
title: Eventos de forma-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas e como formatar eventos para consulta na visualização do Azure time insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 567770c00c645aeb79e1efb0e9119b9ac829f3fe
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861664"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com a pré-visualização do Azure Time Series Insights

Este artigo ajuda você a formatar o arquivo JSON para ingestão e maximizar a eficiência de suas consultas de Azure Time Series Insights Preview.

## <a name="best-practices"></a>Melhores práticas

Pense em como você envia eventos para Time Series Insights visualização. Ou seja, deve sempre:

* envie dados através da rede mais eficientemente possível.
* Store seus dados de forma que o ajuda a agregá-la mais convenientemente para o seu cenário.

Para obter o melhor desempenho de consulta, faça o seguinte:

* Não envie propriedades desnecessárias. Pré-visualização do Time Series Insights cobra-lhe na sua utilização. É melhor armazenar e processar os dados que irá consultar.
* Utilize os campos de instância para dados estáticos. Essa prática ajuda-o a evitar o envio de dados estáticos na rede. Os campos de instância, um componente do modelo de série temporal, funcionam como dados de referência no serviço de Time Series Insights que está geralmente disponível. Para saber mais sobre os campos de instância, leia [modelo de série temporal](./time-series-insights-update-tsm.md).
* Propriedades de dimensão entre dois ou mais eventos de partilha. Essa prática ajuda-o a enviar dados através da rede com mais eficiência.
* Não utilize o aninhamento de matriz profunda. Time Series Insights visualização dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. Pré-visualização do Time Series Insights nivela as matrizes de nas mensagens em vários eventos com pares de valor de propriedade.
* Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

Durante a ingestão, as cargas que contêm aninhamento serão achatadas, de modo que o nome da coluna seja um único valor com um delimitador. Time Series Insights visualização usa sublinhados para delineação. Observe que essa é uma alteração da versão GA do produto que usou períodos. Durante a visualização, há uma limitação em relação ao nivelamento, que é ilustrada no segundo exemplo abaixo.

## <a name="examples"></a>Exemplos

O exemplo seguinte baseia-se um cenário em que dois ou mais dispositivos enviam medições ou sinais. As medidas ou os sinais podem ser *taxa de fluxo*, pressão de óleo do *motor*, *temperatura*e *umidade*.

No exemplo, há uma única mensagem do Hub IoT do Azure em que a matriz externa contém uma seção compartilhada de valores de dimensão comuns. A matriz externa utiliza dados de instâncias de série de tempo para aumentar a eficiência da mensagem. 

A instância de série temporal contém metadados de dispositivo. Esses metadados não são alterados com todos os eventos, mas fornecem propriedades úteis para análise de dados. Para salvar em bytes enviados pela transmissão e tornar a mensagem mais eficiente, considere o envio em lote de valores de dimensão comuns e o uso de metadados de instância de série temporal.

### <a name="example-1"></a>Exemplo 1:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Instância de série de tempo 

> [!NOTE]
> O ID de série de tempo é *deviceId*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Pré-visualização do Time Series Insights une uma tabela (depois de mesclar) durante o tempo de consulta. A tabela inclui colunas adicionais, como **tipo**. O exemplo a seguir demonstra como você pode [Formatar](./time-series-insights-send-events.md#supported-json-shapes) seus dados de telemetria.

| deviceId  | Tipo | L1 | ERROS DE L2 | carimbo de data/hora | Taxa de series_Flow ft3/s | series_Engine de pressão do óleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de bateria | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de bateria |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMUNS | SIMULADOR |    Sistema de bateria |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

No exemplo anterior, tenha em atenção os seguintes pontos:

* Propriedades estáticas são armazenadas no Time Series Insights pré-visualização para otimizar os dados enviados através da rede.
* Time Series Insights dados de visualização são Unidos no momento da consulta por meio da ID de série temporal definida na instância.
* São usadas duas camadas de aninhamento. Esse número é o mais que Time Series Insights visualização dá suporte. É fundamental para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, que são enviados como propriedades separadas dentro do mesmo objeto. No exemplo, **Series_Flow taxa psi**, **Series_Engine psi de pressão do óleo**e **series_Flow taxa ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Os campos de instância não são armazenados com telemetria. Eles são armazenados com metadados no modelo de série temporal.
> A tabela anterior representa a visão de consulta.

### <a name="example-2"></a>Exemplo 2:

Considere o seguinte JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
No exemplo acima, a propriedade de `data_flow` achatada apresentaria uma colisão de nomenclatura com a propriedade `data_flow`. Nesse caso, o valor da propriedade *mais recente* substituiria o anterior. Se esse comportamento apresentar um desafio para seus cenários de negócios, entre em contato com a equipe do TSI.

> [!WARNING] 
> Nos casos em que as propriedades duplicadas estão presentes na mesma carga de evento devido ao nivelamento ou outro mecanismo, o valor mais recente da propriedade é armazenado, overwritting quaisquer valores anteriores.


## <a name="next-steps"></a>Passos seguintes

- Para colocar essas diretrizes em prática, leia [Azure Time Series insights sintaxe de consulta de visualização](./time-series-insights-query-data-csharp.md). Você aprenderá mais sobre a sintaxe de consulta para o Time Series Insights API REST de visualização para acesso a dados.
- Para saber mais sobre as formas JSON com suporte, leia [formas de JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).
