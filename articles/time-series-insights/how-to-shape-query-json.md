---
title: Boas práticas para moldar jSON - Azure Time Series Insights consultas / Microsoft Docs
description: Aprenda a melhorar a eficiência de consulta da Série De Tempo Azure Insights moldando a JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641298"
---
# <a name="shape-json-to-maximize-query-performance"></a>Moldar JSON para maximizar o desempenho da consulta

Este artigo fornece orientações sobre como moldar a JSON para maximizar a eficiência das suas consultas de Insights da Série De Tempo Azure.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Aprenda as melhores práticas para moldar a JSON para atender às suas necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Melhores práticas

Pense em como envia eventos para time series insights. Ou seja, sempre:

1. Envie dados pela rede o mais eficientemente possível.
1. Certifique-se de que os seus dados são armazenados de forma a poder efetuar agregações adequadas para o seu cenário.
1. Certifique-se de que não alcança os limites máximos de propriedade da Time Series Insights:
   - 600 propriedades (colunas) para ambientes S1.
   - 800 propriedades (colunas) para ambientes S2.

> [!TIP]
> Limites de revisão [e planeamento](time-series-insights-update-plan.md) em Visualização de Insights da Série De Tempo Azure.

A seguinte orientação ajuda a garantir o melhor desempenho possível de consulta:

1. Não utilize propriedades dinâmicas, como uma identificação de etiqueta, como nome de propriedade. Esta utilização contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não é necessária, é melhor não enviá-la. Desta forma evita-se limitações de armazenamento.
1. Utilize dados de [referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos sobre a rede.
1. Partilhe propriedades de dimensão entre vários eventos para enviar dados sobre a rede de forma mais eficiente.
1. Não use a nidificação de matrizes profundas. Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. Time Series Insights achata matrizes nas mensagens em vários eventos com pares de valor de propriedade.
1. Se existirem apenas algumas medidas para todos ou a maioria dos eventos, é melhor enviar estas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam de ser processados. Quando existem várias medidas, enviá-las como valores numa única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="example-overview"></a>Visão geral do exemplo

Os dois exemplos seguintes demonstram como enviar eventos para destacar as recomendações anteriores. Seguindo cada exemplo, pode rever a forma como as recomendações foram aplicadas.

Os exemplos baseiam-se num cenário em que vários dispositivos enviam medições ou sinais. Medições ou sinais podem ser Caudal, Pressão do Óleo do Motor, Temperatura e Humidade. No primeiro exemplo, existem algumas medições em todos os dispositivos. O segundo exemplo tem muitos dispositivos, e cada dispositivo envia muitas medições únicas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: Existem apenas algumas medidas

> [!TIP]
> Recomendamos que envie cada medição ou sinal como uma propriedade ou coluna separada.

No exemplo seguinte, há uma única mensagem Azure IoT Hub onde a matriz exterior contém uma secção partilhada de valores de dimensão comum. A matriz exterior utiliza dados de referência para aumentar a eficiência da mensagem. Os dados de referência contêm metadados de dispositivos que não mudam em todos os eventos, mas fornece propriedades úteis para análise de dados. Lotação de valores de dimensão comum e utilização de dados de referência economiza em bytes enviados através do fio, o que torna a mensagem mais eficiente.

Considere a seguinte carga útil da JSON enviada para o seu ambiente Time Series Insights GA utilizando um [objeto de mensagem de dispositivo IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) que é serializado em JSON quando enviado para a nuvem Azure:


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

* Tabela de dados de referência que tem o dispositivo de propriedade **chaveId:**

   | deviceId | mensagemId | dispositivoLocalização |
   | --- | --- | --- |
   | FXXX | DADOS\_DA LINHA | UE |
   | FYYY | DADOS\_DA LINHA | EUA |

* Tabela de eventos Time Series Insights, após achatamento:

   | deviceId | mensagemId | dispositivoLocalização | carimbo de data/hora | série. Caudal ft3/s | série. Pressão do Óleo do Motor psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | DADOS\_DA LINHA | UE | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | DADOS\_DA LINHA | UE | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | DADOS\_DA LINHA | EUA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - A coluna id do **dispositivo** serve como cabeçalho da coluna para os vários dispositivos de uma frota. Fazer com que o **dispositivo ValorIo** lógico o seu próprio nome de propriedade limita o total dos dispositivos a 595 (para ambientes S1) ou 795 (para ambientes S2) com as outras cinco colunas.
> - Propriedades desnecessárias são evitadas (por exemplo, a informação de fazer e modelo). Como as propriedades não serão questionadas no futuro, eliminá-las permite uma melhor eficiência de rede e armazenamento.
> - Os dados de referência são utilizados para reduzir o número de bytes transferidos através da rede. Os dois atributos **messageId** e **dispositivoLocalização** são unidos utilizando o dispositivo de propriedade **chaveId**. Estes dados são unidos com os dados de telemetria no momento da entrada e são depois armazenados em Time Series Insights para consulta.
> - São utilizadas duas camadas de nidificação, que é a quantidade máxima de nidificação suportada pela Time Series Insights. É fundamental evitar matrizes profundamente aninhadas.
> - As medidas são enviadas como propriedades separadas dentro do mesmo objeto porque existem poucas medidas. Aqui, **séries. Flow Rate psi** e **séries. Pressão do óleo do motor ft3/s** são colunas únicas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> Recomendamos que envie medições como "tipo", "unidade" e tuples de "valor".

Exemplo JSON carga útil:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Tabela de dados de referência que tem as propriedades chave **dispositivoId** e **série.tagId**:

   | deviceId | série.tagId | mensagemId | dispositivoLocalização | tipo | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_DA LINHA | UE | Caudal | ft3/s |
   | FXXX | óleoPressão | DADOS\_DA LINHA | UE | Pressão do óleo do motor | psi |
   | FYYY | pumpRate | DADOS\_DA LINHA | EUA | Caudal | ft3/s |
   | FYYY | óleoPressão | DADOS\_DA LINHA | EUA | Pressão do óleo do motor | psi |

* Tabela de eventos Time Series Insights, após achatamento:

   | deviceId | série.tagId | mensagemId | dispositivoLocalização | tipo | unit | carimbo de data/hora | série.valor |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_DA LINHA | UE | Caudal | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | óleoPressão | DADOS\_DA LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | DADOS\_DA LINHA | UE | Caudal | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | óleoPressão | DADOS\_DA LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | DADOS\_DA LINHA | EUA | Caudal | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | óleoPressão | DADOS\_DA LINHA | EUA | Pressão do óleo do motor | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - O dispositivo de **colunasId** e **série.tagId** servem como cabeçalhos de coluna para os vários dispositivos e tags numa frota. A utilização de cada um como atributo limita a consulta a 594 (para ambientes S1) ou 794 (para ambientes S2) total de dispositivos com as outras seis colunas.
> - Foram evitadas propriedades desnecessárias, pela razão citada no primeiro exemplo.
> - Os dados de referência são utilizados para reduzir o número de bytes transferidos através da rede através da introdução do **dispositivoId**, que é utilizado para o par único de **mensagensId** e **dispositivoSLocalização**. A série de chaves **compostas.tagId** é utilizada para o par único de **tipo** e **unidade**. A chave composta permite que o **par id** e **série.tagId** seja utilizado para se referir a quatro valores: **messageId, deviceLocation, tipo** e **unidade**. Estes dados juntam-se aos dados de telemetria no momento da entrada. É então armazenado em Time Series Insights para consulta.
> - São utilizadas duas camadas de nidificação, pela razão citada no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Para uma propriedade com um grande número de valores possíveis, o melhor é enviar como valores distintos dentro de uma única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

  - No primeiro exemplo, algumas propriedades têm vários valores, por isso é apropriado fazer cada uma propriedade separada.
  - No segundo exemplo, as medidas não são especificadas como propriedades individuais. Em vez disso, são uma variedade de valores ou medidas sob uma propriedade comum da série. É enviado o novo **tagId** chave, que cria a nova série de **colunas.tagId** na tabela achatada. O **tipo** e **unidade** de novas propriedades são criados usando dados de referência para que o limite de propriedade não seja atingido.

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre o envio de mensagens de [dispositivo IoT Hub para a nuvem](../iot-hub/iot-hub-devguide-messages-construct.md).

- Leia a [sintaxe](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) de consulta da Azure Time Series Insights para saber mais sobre a sintaxe de consulta para os dados da Time Series Insights aceder à REST API.

- Aprenda [a moldar eventos.](./time-series-insights-send-events.md)
