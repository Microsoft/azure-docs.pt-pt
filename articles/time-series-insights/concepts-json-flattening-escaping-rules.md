---
title: JSON achatando e escapando regras - Azure Time Series Insights Gen2 / Microsoft Docs
description: Saiba mais sobre o achatamento, fuga e manuseamento de matrizes em Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: a1f633548ed36320f40e485f540923c8e3045a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460871"
---
# <a name="json-flattening-escaping-and-array-handling"></a>Simplificação, Escape e Processamento de Matriz do JSON

O seu ambiente Azure Time Series Insights Gen2 criará dinamicamente as colunas dos seus armazéns quentes e frios, seguindo um conjunto particular de convenções de nomeação. Quando um evento é ingerido, um conjunto de regras é aplicado à carga útil JSON e nomes de propriedade. Estes incluem escapar de certos caracteres especiais e achatar objetos JSON aninhados. É importante conhecer estas regras para que compreenda como a forma do seu JSON influenciará a forma como os seus eventos são armazenados e questionados. Consulte a tabela abaixo para ver a lista completa de regras. Exemplos A & B também demonstram como é capaz de lotar séries de tempo múltiplas de forma eficiente numa matriz.

> [!IMPORTANT]
>
> * Reveja as regras abaixo antes de selecionar uma [propriedade de ID da Série De Tempo](time-series-insights-update-how-to-id.md) e/ou o seu timetamp de origem de [eventos(ies)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). Se o seu ID TS ou o timetamp estiverem dentro de um objeto aninhado ou tiver um ou mais caracteres especiais abaixo, é importante garantir que o nome da propriedade que fornece corresponde ao nome da coluna *após* a aplicação das regras de ingestão. Veja o exemplo [B](concepts-json-flattening-escaping-rules.md#example-b) abaixo.

| Regra | Exemplo JSON | [Sintaxe de expressão de série de tempo](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nome da coluna de propriedade em Parquet
|---|---|---|---|
| O tipo de dados Azure Time Series Insights Gen2 é anexado ao final do seu nome de coluna como "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| A [propriedade de timetamp](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) de fonte de evento será guardada em Azure Time Series Insights Gen2 como "timetamp" em armazenamento, e o valor armazenado na UTC. Pode personalizar a propriedade de marca de tempo de fonte de evento para atender às necessidades da sua solução, mas o nome da coluna em armazenamento quente e frio é "timetamp". Outras propriedades JSON de data que não sejam o ponto de tempo de origem do evento serão guardadas com "_datetime" no nome da coluna, conforme mencionado na regra acima.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Nomes de propriedade JSON que incluem os caracteres especiais. [ \ e ' são escapadas usando [' e ']  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| Dentro de [' e '] há escapatória adicional de citações individuais e backslashes. Uma única citação será escrita como \' e um backslash será escrito como \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Os objetos JSON aninhados são achatados com um período como separador. A nidificação até 10 níveis é suportada. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` ou `$event.series['value'].Long` |  `series.value_long` |
| Matrizes de tipos primitivos são armazenados como o tipo Dinâmico |  ```"values": [154, 149, 147]``` | Os tipos dinâmicos só podem ser recuperados através da [API GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| As matrizes que contêm objetos têm dois comportamentos dependendo do conteúdo do objeto: Se a propriedade de TS ID ou timetamp(ies) estiver dentro dos objetos de uma matriz, a matriz será desenrolada de modo a que a carga útil inicial do JSON produza múltiplos eventos. Isto permite-lhe aloisar vários eventos numa estrutura JSON. Quaisquer propriedades de alto nível que sejam pares para a matriz serão guardadas com cada objeto desenrolado. Se o seu(s) TS ID(s) e a sua estampada não *estiverem* dentro da matriz, será guardado inteiro como o tipo Dinâmico. | Veja os exemplos [A,](concepts-json-flattening-escaping-rules.md#example-a) [B](concepts-json-flattening-escaping-rules.md#example-b)e [C](concepts-json-flattening-escaping-rules.md#example-c) abaixo
| As matrizes que contêm elementos mistos não são achatadas. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Os tipos dinâmicos só podem ser recuperados através da [API GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| 512 caracteres é o limite de nome de propriedade JSON. Se o nome exceder 512 caracteres, será truncado para 512 e '_<'hashCode'>' é anexado. **Note** que isto também se aplica a nomes de propriedade que foram concatenados a partir de objeto achatado, denotando um caminho de objeto aninhado. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Compreender o duplo comportamento para matrizes

Conjuntos de objetos serão armazenados inteiros ou divididos em vários eventos, dependendo da forma como modelou os seus dados. Isto permite-lhe utilizar uma matriz para lotar eventos e evitar a repetição de propriedades de telemetria que são definidas ao nível do objeto raiz. O lote pode ser vantajoso, uma vez que resulta em menos mensagens de Event Hubs ou IoT Hub enviadas.

No entanto, em alguns casos, as matrizes que contêm objetos só têm significado no contexto de outros valores. Criar vários eventos tornaria os dados sem sentido. Para garantir que uma matriz de objetos é armazenada como um tipo dinâmico, siga a orientação de modelação de dados abaixo e dê uma olhada no [exemplo C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Como saber se a minha variedade de objetos produzirá vários eventos

Se um ou mais dos seus iDs de série de tempo estiver aninhado dentro de objetos numa matriz, *ou* se a propriedade do timetamp de fonte de evento estiver aninhada, o motor de ingestão irá dividi-lo para criar múltiplos eventos. Os nomes de propriedade que forneceu para o seu(s) TS ID(s) e/ou timetamp devem seguir as regras de achatamento acima, e, portanto, indicará a forma do seu JSON. Veja os exemplos abaixo e consulte o guia sobre como [selecionar uma propriedade de ID da Série De Tempo.](time-series-insights-update-how-to-id.md)

### <a name="example-a"></a>Exemplo A

ID série de tempo na raiz do objeto e relógio aninhado\
**ID da Série de Tempo do Ambiente:**`"id"`\
**Data de tempo de fonte de evento:**`"values.time"`\
**Carga JSON:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Resultado em arquivo Parquet:**\
A configuração e carga útil acima produzirá três colunas e quatro eventos

| carimbo de data/hora  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Exemplo B

ID compósito da Série de Tempo com uma propriedade aninhada\
**ID da Série de Tempo do Ambiente:** `"plantId"` e `"telemetry.tagId"`\
**Data de tempo de fonte de evento:**`"timestamp"`\
**Carga JSON:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Resultado em arquivo Parquet:**\
A configuração e carga útil acima produzirá quatro colunas e seis eventos

| carimbo de data/hora  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>Exemplo C

Time Series ID e timetamp estão na raiz do objeto\
**ID da Série de Tempo do Ambiente:**`"id"`\
**Data de tempo de fonte de evento:**`"timestamp"`\
**Carga JSON:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Resultado em arquivo Parquet:**\
A configuração e carga útil acima produzirá três colunas e um evento

| carimbo de data/hora  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Passos seguintes

* Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente
