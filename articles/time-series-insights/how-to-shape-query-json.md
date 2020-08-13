---
title: Boas práticas para moldar consultas JSON - Azure Time Series Insights Microsoft Docs
description: Aprenda a melhorar a eficiência da sua série de tempo Azure, moldando o JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1a7a88e0db38f399dc47c030f3b97f6b26f4da07
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168240"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>Moldar JSON para maximizar o desempenho da consulta no seu ambiente Gen1

Este artigo fornece orientações sobre como moldar o JSON para maximizar a eficiência das suas consultas Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Aprenda as melhores práticas para moldar o JSON para atender às suas necessidades de armazenamento.</br>

> [!VIDEO <https://www.youtube.com/embed/b2BD5hwbg5I>]

## <a name="best-practices"></a>Melhores práticas

Pense em como envia eventos para a Azure Time Series Insights. Ou seja, sempre:

1. Envie dados pela rede o mais eficientemente possível.
1. Certifique-se de que os seus dados são armazenados de forma a poder realizar agregações adequadas ao seu cenário.
1. Certifique-se de que não atinge os limites máximos de propriedade do Azure Time Series Insights de:
   - 600 propriedades (colunas) para ambientes S1.
   - 800 propriedades (colunas) para ambientes S2.

As seguintes orientações ajudam a garantir o melhor desempenho possível da consulta:

1. Não utilize propriedades dinâmicas, como um ID de identificação, como um nome de propriedade. Esta utilização contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não é necessária, é melhor não enviá-la. Desta forma evita limitações de armazenamento.
1. Utilize [dados de referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos sobre a rede.
1. Partilhe propriedades de dimensão entre vários eventos para enviar dados sobre a rede de forma mais eficiente.
1. Não use nidificação de matrizes profundas. Azure Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. Azure Time Series Insights achata matrizes nas mensagens em múltiplos eventos com pares de valor de propriedade.
1. Se existem apenas algumas medidas para todos ou a maioria dos eventos, é melhor enviar estas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados. Quando há várias medidas, enviá-las como valores numa única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="example-overview"></a>Visão geral do exemplo

Os dois exemplos que se seguem demonstram como enviar eventos para destacar as recomendações anteriores. Seguindo cada exemplo, pode rever a forma como as recomendações foram aplicadas.

Os exemplos baseiam-se num cenário em que vários dispositivos enviam medições ou sinais. As medições ou sinais podem ser o caudal, a pressão do óleo do motor, a temperatura e a humidade. No primeiro exemplo, existem algumas medições em todos os dispositivos. O segundo exemplo tem muitos dispositivos, e cada dispositivo envia muitas medições únicas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: Existem apenas algumas medidas

> [!TIP]
> Recomendamos que envie cada medida ou sinal como uma propriedade ou coluna separada.

No exemplo seguinte, há uma única mensagem Azure IoT Hub onde a matriz exterior contém uma secção partilhada de valores de dimensão comum. A matriz exterior utiliza dados de referência para aumentar a eficiência da mensagem. Os dados de referência contêm metadados do dispositivo que não mudam em todos os eventos, mas fornecem propriedades úteis para análise de dados. O loteamento de valores de dimensão comum e a utilização de dados de referência economizam em bytes enviados através do fio, o que torna a mensagem mais eficiente.

Considere a seguinte carga JSON enviada para o seu ambiente Azure Time Series Insights GA utilizando um [objeto de mensagem de dispositivo IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) que é serializado em JSON quando enviado para a nuvem Azure:

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

- Tabela de dados de referência que tem o dispositivo de propriedade **chaveId**:

   | deviceId | messageId | dispositivoLocalização |
   | --- | --- | --- |
   | FXXX | \_DADOS DE LINHA | UE |
   | FYYY | \_DADOS DE LINHA | EUA |

- Tabela de eventos Azure Time Series Insights, depois de achatamento:

   | deviceId | messageId | dispositivoLocalização | carimbo de data/hora | série. Caudal ft3/s | série. Pressão do Óleo do Motor psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | \_DADOS DE LINHA | UE | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | \_DADOS DE LINHA | UE | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | \_DADOS DE LINHA | EUA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]

> - A coluna **deviceId** serve como cabeçalho de coluna para os vários dispositivos de uma frota. Fazer com que o **dispositivoDo** valor do seu próprio nome de propriedade limite o total de dispositivos para 595 (para ambientes S1) ou 795 (para ambientes S2) com as outras cinco colunas.
> - São evitadas propriedades desnecessárias (por exemplo, a informação de 2000 e modelos). Como as propriedades não serão questionadas no futuro, eliminá-las permite uma melhor eficiência de rede e armazenamento.
> - Os dados de referência são utilizados para reduzir o número de bytes transferidos através da rede. Os dois atributos **messageId** e **deviceLocation** são unidos usando o dispositivo de propriedade **chaveId**. Estes dados são associados aos dados da telemetria no momento de entrada e são depois armazenados em Azure Time Series Insights para consulta.
> - São utilizadas duas camadas de nidificação, que é a quantidade máxima de nidificação suportada pela Azure Time Series Insights. É fundamental evitar matrizes profundamente aninhadas.
> - As medidas são enviadas como propriedades separadas dentro do mesmo objeto porque existem poucas medidas. Aqui, **série. Flow Rate psi** e **séries. Motor Oil Pressure ft3/s** são colunas únicas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> Recomendamos que envie medições como tuples "tipo", "unidade" e "valor".

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

- Tabela de dados de referência que tem as propriedades-chave **dispositivoId** e **série.tagId**:

   | deviceId | series.tagId | messageId | dispositivoLocalização | tipo | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | bombearRate | \_DADOS DE LINHA | UE | Caudal | ft3/s |
   | FXXX | óleoPressão | \_DADOS DE LINHA | UE | Pressão do óleo do motor | psi |
   | FYYY | bombearRate | \_DADOS DE LINHA | EUA | Caudal | ft3/s |
   | FYYY | óleoPressão | \_DADOS DE LINHA | EUA | Pressão do óleo do motor | psi |

- Tabela de eventos Azure Time Series Insights, depois de achatamento:

   | deviceId | series.tagId | messageId | dispositivoLocalização | tipo | unit | carimbo de data/hora | série.valor |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | bombearRate | \_DADOS DE LINHA | UE | Caudal | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
   | FXXX | óleoPressão | \_DADOS DE LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | bombearRate | \_DADOS DE LINHA | UE | Caudal | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
   | FXXX | óleoPressão | \_DADOS DE LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | bombearRate | \_DADOS DE LINHA | EUA | Caudal | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | óleoPressão | \_DADOS DE LINHA | EUA | Pressão do óleo do motor | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]

> - Os **dispositivos** de colunasId e **série.tagId** servem como cabeçalhos de coluna para os vários dispositivos e tags numa frota. A utilização de cada um como seu atributo limita a consulta a 594 (para ambientes S1) ou 794 (para ambientes S2) dispositivos totais com as outras seis colunas.
> - Foram evitadas propriedades desnecessárias, pela razão citada no primeiro exemplo.
> - Os dados de referência são utilizados para reduzir o número de bytes transferidos através da rede, introduzindo **o deviceId**, que é utilizado para o par único de **mensagensId** e **deviceLocation**. A **série-chave composta.tagId** é utilizada para o par único de **tipo** e **unidade**. A tecla composta permite que o par **deviceId** e **série.tagId** seja utilizado para se referir a quatro valores: **messageId, deviceLocation, type** e **unit**. Estes dados são associados aos dados da telemetria no momento de entrada. É então armazenado em Azure Time Series Insights para consulta.
> - São utilizadas duas camadas de nidificação, pela razão citada no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Para uma propriedade com um grande número de valores possíveis, é melhor enviar como valores distintos dentro de uma única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

- No primeiro exemplo, algumas propriedades têm vários valores, por isso é apropriado fazer cada uma de uma propriedade separada.
- No segundo exemplo, as medidas não são especificadas como propriedades individuais. Em vez disso, são uma variedade de valores ou medidas sob uma propriedade comum da série. O novo **tagId** da chave é enviado, que cria a nova série de **colunas.tagId** na tabela achatada. O **novo tipo** e **unidade** de propriedades são criados usando dados de referência para que o limite de propriedade não seja atingido.

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre o envio de [mensagens de dispositivo IoT Hub para a nuvem](../iot-hub/iot-hub-devguide-messages-construct.md).

- Leia [Azure Time Series Insights consulta sintaxe](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-syntax) para saber mais sobre a sintaxe de consulta para o Azure Time Series Insights acesso dados REST API.

- Aprenda [a moldar eventos.](./time-series-insights-send-events.md)
