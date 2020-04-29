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
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650928"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Moldar eventos com pré-visualização de insights da série de tempo azure

Este artigo define as melhores práticas para moldar as suas cargas JSON para ingestão em Insights da Série Relógio Azure e maximizar a eficiência das suas consultas de pré-visualização.

## <a name="best-practices"></a>Melhores práticas

É melhor considerar cuidadosamente como envia eventos para o seu ambiente de pré-visualização da Série De Tempo Insights. 

As melhores práticas gerais incluem:

* Envie dados pela rede o mais eficientemente possível.
* Guarde os seus dados de uma forma que o ajude a agregar-os de forma mais adequada para o seu cenário.

Para obter o melhor desempenho de consulta, adere às seguintes regras do polegar:

* Não envie propriedades desnecessárias. Time Series Insights Preview bills por utilização. É melhor armazenar e processar apenas os dados que irá consultar.
* Utilize campos de exemplo para dados estáticos. Esta prática ajuda a evitar o envio de dados estáticos através da rede. Os campos de instância, um componente do Modelo série de tempo, funcionam como dados de referência no serviço Time Series Insights que está geralmente disponível. Para saber mais sobre os campos de exemplo, leia o [Modelo da Série De Tempo.](./time-series-insights-update-tsm.md)
* Partilhe propriedades de dimensão entre dois ou mais eventos. Esta prática ajuda-o a enviar dados através da rede de forma mais eficiente.
* Não use a nidificação de matrizes profundas. Time Series Insights Preview suporta até dois níveis de matrizes aninhadas que contêm objetos. Time Series Insights Preview achata matrizes em mensagens em vários eventos com pares de valor de propriedade.
* Se existirem apenas algumas medidas para todos ou a maioria dos eventos, é melhor enviar estas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam de ser processados.

## <a name="column-flattening"></a>Achatamento de colunas

Durante a ingestão, as cargas que contenham objetos aninhados serão achatadas de modo a que o nome da coluna seja um único valor com um delineador.

* Por exemplo, o seguinte JSON aninhado:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Torna-se: `data_flow` quando achatado.

> [!IMPORTANT]
> * A Pré-visualização da Série`_`de Tempo Azure insights utiliza sublinha () para a delimitação da coluna.
> * Note a diferença de Disponibilidade Geral`.`que utiliza períodos ( ) em vez disso.

Cenários mais complexos são ilustrados abaixo.

#### <a name="example-1"></a>Exemplo 1:

O seguinte cenário tem dois (ou mais) dispositivos que enviam as medições (sinais): *Caudal,* *Pressão do Óleo do Motor,* *Temperatura*e *Humidade*.

Há uma única mensagem Azure IoT Hub enviada onde a matriz exterior contém uma secção partilhada de valores de dimensão comum (note as duas entradas do dispositivo contidas na mensagem).

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

**Takeaways:**

* O exemplo JSON tem uma matriz exterior que usa dados da [Time Series Instance](./time-series-insights-update-tsm.md#time-series-model-instances) para aumentar a eficiência da mensagem. Embora os metadados do dispositivo Time Series Instances não sejam suscetíveis de mudar, muitas vezes fornece propriedades úteis para análise de dados.

* O JSON combina duas ou mais mensagens (uma de cada dispositivo) numa única poupança de carga útil na largura de banda ao longo do tempo.

* Os pontos de dados individuais das séries para cada dispositivo são combinados numa única **série** atribuindo a necessidade de transmitir continuamente atualizações para cada dispositivo.

> [!TIP]
> Para reduzir o número de mensagens necessárias para enviar dados e tornar a telemetria mais eficiente, considere o lote de valores de dimensão comum e os metadados da Time Series Instance numa única carga útil da JSON.

#### <a name="time-series-instance"></a>Instância da Série De Tempo 

Vamos ver mais de perto como usar a [Time Series Instance](./time-series-insights-update-tsm.md#time-series-model-instances) para moldar o seu JSON de forma mais ótima. 

> [!NOTE]
> Os [IDs](./time-series-insights-update-how-to-id.md) da Série de Tempo abaixo são *dispositivoIds*.

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

Time Series Insights Preview junta-se a uma tabela (após achatamento) durante o tempo de consulta. A tabela inclui colunas adicionais, tais como **Tipo**.

| deviceId  | Tipo | L1 | L2 | carimbo de data/hora | taxa de series_Flow ft3/s | series_Engine Pressão do Petróleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de Baterias | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de Baterias |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMMON | SIMULADOR |    Sistema de Baterias |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  A tabela anterior representa a vista de consulta no Explorador de [Pré-visualização](./time-series-insights-update-explorer.md)da Série de Tempo.

**Takeaways:**

* No exemplo anterior, as propriedades estáticas são armazenadas na Time Series Insights Preview para otimizar os dados enviados sobre a rede.
* Os dados de pré-visualização da Série Time Insights são unidos no momento da consulta através do ID da Série de Tempo que é definido no caso.
* São utilizadas duas camadas de nidificação. Este número é o que mais suporta a Pré-Visualização de Insights da Série De Tempo. É fundamental evitar matrizes profundamente aninhadas.
* Como há poucas medidas, são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **series_Flow Rate PSI**, series_Engine Oil Pressure **psi**, e series_Flow **Rate ft3/s** são colunas únicas.

>[!IMPORTANT]
> Os campos de exemplo não estão armazenados com telemetria. Estão armazenados com metadados no Modelo da Série Tempo.

#### <a name="example-2"></a>Exemplo 2:

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

No exemplo acima, a `data["flow"]` propriedade achatada `data_flow` apresentaria uma colisão de nomeação com a propriedade.

Neste caso, o *valor mais recente* da propriedade substituiria o anterior. 

> [!TIP]
> Contacte a equipa time series insights para obter mais assistência!

> [!WARNING] 
> * Nos casos em que as propriedades duplicadas estão presentes na mesma (singular) carga útil do evento devido a achatamento ou outro mecanismo, o valor mais recente > propriedade é armazenado, sobreescrevendo quaisquer valores anteriores.
> * Séries de eventos combinados não se sobreporão uns aos outros.

## <a name="next-steps"></a>Passos seguintes

* Para pôr estas diretrizes em prática, leia a [sintaxe de pré-visualização](./time-series-insights-query-data-csharp.md)da Azure Time Series Insights . Você saberá mais sobre a sintaxe de consulta para a Time Series Insights [Preview REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) para acesso a dados.

* Combine as melhores práticas da JSON com [o Modelo de Série seleção.](./time-series-insights-update-how-to-tsm.md)
