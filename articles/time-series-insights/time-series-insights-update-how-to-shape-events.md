---
title: Eventos de forma - Azure Time Series Insights [ Azure Time Series Insights ] Microsoft Docs
description: Saiba mais sobre as melhores práticas e como moldar eventos para consulta em Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: e814d9be4a0db2852bd9e21f3d3c1d54a45bd268
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368637"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com a pré-visualização do Azure Time Series Insights

Este artigo ajuda-o a moldar o seu ficheiro JSON para ingestão e a maximizar a eficiência das suas consultas de pré-visualização da Série de Tempo Azure.

## <a name="best-practices"></a>Melhores práticas

Pense em como envia eventos para a Time Series Insights Preview. Ou seja, deve sempre:

* envie dados através da rede mais eficientemente possível.
* Store seus dados de forma que o ajuda a agregá-la mais convenientemente para o seu cenário.

Para obter a melhor performance de consulta, faça o seguinte:

* Não envie propriedades desnecessárias. Pré-visualização do Time Series Insights cobra-lhe na sua utilização. É melhor armazenar e processar os dados que irá consultar.
* Utilize os campos de instância para dados estáticos. Essa prática ajuda-o a evitar o envio de dados estáticos na rede. Os campos de instância, um componente do Modelo série de tempo, funcionam como dados de referência no serviço Time Series Insights que está geralmente disponível. Para saber mais sobre os campos de exemplo, leia o [Modelo da Série De Tempo.](./time-series-insights-update-tsm.md)
* Propriedades de dimensão entre dois ou mais eventos de partilha. Essa prática ajuda-o a enviar dados através da rede com mais eficiência.
* Não utilize o aninhamento de matriz profunda. Time Series Insights Preview suporta até dois níveis de matrizes aninhadas que contêm objetos. Pré-visualização do Time Series Insights nivela as matrizes de nas mensagens em vários eventos com pares de valor de propriedade.
* Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam de ser processados.

Durante a ingestão, as cargas que contenham nidificação serão achatadas, de modo a que o nome da coluna seja um único valor com um delineador. Time Series Insights Preview utiliza sublinhados para delineação. Note que esta é uma alteração da versão GA do produto que utilizou períodos. Durante a pré-visualização, há uma ressalva em torno do achatamento, que é ilustrado no segundo exemplo abaixo.

## <a name="examples"></a>Exemplos

O exemplo seguinte baseia-se um cenário em que dois ou mais dispositivos enviam medições ou sinais. As medições ou sinais podem ser *caudal,* *pressão do óleo do motor,* *temperatura*e *humidade.*

No exemplo, há uma única mensagem Azure IoT Hub onde a matriz exterior contém uma secção partilhada de valores de dimensão comum. A matriz externa utiliza dados de instâncias de série de tempo para aumentar a eficiência da mensagem. 

A Instância da Série de Tempo contém metadados do dispositivo. Estes metadados não mudam em todos os eventos, mas fornecem propriedades úteis para análise de dados. Para poupar bytes enviados sobre o fio e tornar a mensagem mais eficiente, considere o lote de valores de dimensão comuns e utilizando metadados da Time Series Instance.

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
> O ID da Série De Tempo é *dispositivoId*.

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

Pré-visualização do Time Series Insights une uma tabela (depois de mesclar) durante o tempo de consulta. A tabela inclui colunas adicionais, tais como **Tipo**. O exemplo que se segue demonstra como pode [moldar](./time-series-insights-send-events.md#supported-json-shapes) os seus dados de telemetria.

| deviceId  | Tipo | L1 | ERROS DE L2 | carimbo de data/hora | taxa de series_Flow ft3/s | series_Engine Pressão do Petróleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de bateria | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de bateria |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMUNS | SIMULADOR |    Sistema de bateria |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

No exemplo anterior, tenha em atenção os seguintes pontos:

* Propriedades estáticas são armazenadas no Time Series Insights pré-visualização para otimizar os dados enviados através da rede.
* Os dados de pré-visualização da Série Time Insights são unidos no momento da consulta através do ID da Série de Tempo que é definido no caso.
* São utilizadas duas camadas de nidificação. Este número é o que mais suporta a Pré-Visualização de Insights da Série De Tempo. É fundamental para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, que são enviados como propriedades separadas dentro do mesmo objeto. No exemplo, **series_Flow Rate PSI**, series_Engine Oil Pressure **psi**, e series_Flow **Rate ft3/s** são colunas únicas.

>[!IMPORTANT]
> Os campos de exemplo não estão armazenados com telemetria. Estão armazenados com metadados no Modelo da Série Tempo.
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
No exemplo acima, a propriedade `data_flow` achatada apresentaria uma colisão de nomeação com a propriedade `data_flow`. Neste caso, o *valor mais recente* da propriedade substituiria o anterior. Se este comportamento apresentar um desafio para os seus cenários de negócio, contacte a equipa da TSI.

> [!WARNING] 
> Nos casos em que as propriedades duplicadas estão presentes no mesmo evento, a carga útil devido a achatamento ou outro mecanismo, o valor mais recente da propriedade é armazenado, sobrecarregando quaisquer valores anteriores.


## <a name="next-steps"></a>Passos seguintes

Para pôr estas diretrizes em prática, leia a [sintaxe de pré-visualização](./time-series-insights-query-data-csharp.md)da Azure Time Series Insights . Você saberá mais sobre a sintaxe de consulta para a Time Series Insights Preview REST API para acesso a dados.
