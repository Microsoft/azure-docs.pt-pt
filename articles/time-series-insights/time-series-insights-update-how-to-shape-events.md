---
title: Eventos de forma - Azure Time Series Insights / Microsoft Docs
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
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036989"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Eventos de forma com Azure Time Series Insights Preview

Este artigo define as melhores práticas para moldar as suas cargas JSON para ingestão em Azure Time Series Insights e para maximizar a eficiência das suas consultas de pré-visualização.

## <a name="best-practices"></a>Melhores práticas

É melhor considerar cuidadosamente como envia eventos para o seu ambiente de visualização de insights de séries de tempo. 

As melhores práticas gerais incluem:

* Envie dados pela rede o mais eficientemente possível.
* Guarde os seus dados de forma a ajudá-lo a agregar-nos de forma mais adequada para o seu cenário.

Para obter o melhor desempenho de consulta, respeite as seguintes regras do polegar:

* Não envie propriedades desnecessárias. Séries de tempo Insights Pré-visualizar contas por utilização. É melhor armazenar e processar apenas os dados que irá consultar.
* Utilize campos de exemplo para dados estáticos. Esta prática ajuda a evitar o envio de dados estáticos através da rede. Os campos de exemplo, um componente do Modelo série de tempo, funcionam como dados de referência no serviço Time Series Insights que está geralmente disponível. Para saber mais sobre os campos de exemplo, leia [o Modelo série de tempo.](./concepts-model-overview.md)
* Partilhar propriedades de dimensão entre dois ou mais eventos. Esta prática ajuda-o a enviar dados através da rede de forma mais eficiente.
* Não use nidificação de matrizes profundas. A previsão de insights da Série de Tempo suporta até dois níveis de matrizes aninhadas que contêm objetos. Time Series Insights Preview achata matrizes em mensagens em múltiplos eventos com pares de valor de propriedade.
* Se existem apenas algumas medidas para todos ou a maioria dos eventos, é melhor enviar estas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

## <a name="column-flattening"></a>Achatamento da coluna

Durante a ingestão, as cargas que contenham objetos aninhados serão achatadas de modo a que o nome da coluna seja um único valor com um delineador.

* Por exemplo, o seguinte aninhado JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Torna-se: `data_flow` quando achatado.

> [!IMPORTANT]
> * Azure Time Series Insights Preview usa sublinhados ( `_` ) para a delimitação de colunas.
> * Note a diferença da Disponibilidade Geral que utiliza os períodos `.` () em vez disso.

Cenários mais complexos são ilustrados abaixo.

#### <a name="example-1"></a>Exemplo 1:

O seguinte cenário tem dois (ou mais) dispositivos que enviam as medições (sinais): *Caudal, Pressão* *do Óleo do Motor,* *Temperatura*e *Humidade*.

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

* O exemplo JSON tem uma matriz externa que usa dados [de Séries de Tempo](./concepts-model-overview.md#time-series-model-instances) para aumentar a eficiência da mensagem. Embora as séries de tempo os metadados do dispositivo não são suscetíveis de alterar, muitas vezes fornece propriedades úteis para análise de dados.

* O JSON combina duas ou mais mensagens (uma de cada dispositivo) numa única carga útil que poupa em largura de banda ao longo do tempo.

* Os pontos de dados individuais da série para cada dispositivo são combinados num único atributo **de série,** reduzindo a necessidade de transmitir continuamente atualizações para cada dispositivo.

> [!TIP]
> Para reduzir o número de mensagens necessárias para enviar dados e tornar a telemetria mais eficiente, considere o loteamento de valores de dimensão comum e metadados de instância de série de tempo numa única carga útil JSON.

#### <a name="time-series-instance"></a>Instância da Série de Tempo 

Vamos ver mais de perto como usar a [Time Series Instance](./concepts-model-overview.md#time-series-model-instances) para moldar o seu JSON de forma mais otimizada. 

> [!NOTE]
> Os [IDs da Série De Tempo](./time-series-insights-update-how-to-id.md) abaixo são *dispositivosIds*.

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

A Previsão de Insights de Séries de Tempo junta-se a uma tabela (depois de achatamento) durante o tempo de consulta. A tabela inclui colunas adicionais, tais como **Tipo**.

| deviceId  | Tipo | L1 | L2 | carimbo de data/hora | taxa series_Flow ft3/s | series_Engine Pressão do Petróleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de Baterias | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de Baterias |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA COMUM | SIMULADOR |    Sistema de Baterias |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  A tabela anterior representa a visualização de consulta no Explorador de [Pré-visualização da Série Temporal](./time-series-insights-update-explorer.md).

**Takeaways:**

* No exemplo anterior, as propriedades estáticas são armazenadas na Preview Time Series Insights para otimizar os dados enviados pela rede.
* Os dados de pré-visualização de Séries de Tempo são unidos no tempo de consulta através do ID da Série de Tempo que é definido no caso.
* São utilizadas duas camadas de nidificação. Este número é o máximo que a Previsão de Insights de Séries Tempos suporta. É fundamental evitar matrizes profundamente aninhadas.
* Como há poucas medidas, são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **series_Flow Rate psi**, series_Engine Oil Pressure **psi**, e series_Flow **Rate ft3/s** são colunas únicas.

>[!IMPORTANT]
> Os campos de exemplo não são armazenados com telemetria. São armazenados com metadados no modelo da Série Tempo.

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

No exemplo acima, a propriedade achatada `data["flow"]` apresentaria uma colisão de nomeação com a `data_flow` propriedade.

Neste caso, o *valor mais recente* da propriedade substituiria o anterior. 

> [!TIP]
> Contacte a equipa de Insights da Série De Tempo para obter mais assistência!

> [!WARNING] 
> * Nos casos em que as propriedades duplicadas estão presentes na mesma carga útil (singular) do evento devido ao achatamento ou a outro mecanismo, o valor mais recente > propriedade é armazenado, sobre-escrevendo quaisquer valores anteriores.
> * Uma série de eventos combinados não se sobrepõem uns aos outros.

## <a name="next-steps"></a>Próximos passos

* Para pôr em prática estas diretrizes, leia [a sintaxe de visualização da série de tempo Azure.](./time-series-insights-query-data-csharp.md) Você saberá mais sobre a sintaxe de consulta para a [API de pré-visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) de insights de séries temporais para acesso a dados.

* Combine as melhores práticas da JSON com [o Modelo de Séries Temporítem.](./time-series-insights-update-how-to-tsm.md)
