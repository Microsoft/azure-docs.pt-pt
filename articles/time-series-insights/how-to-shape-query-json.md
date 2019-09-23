---
title: Práticas recomendadas para o Shaping de JSON em consultas Azure Time Series Insights | Microsoft Docs
description: Saiba como melhorar sua eficiência de consulta do Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 08/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 48e09a64812f7552bd79c529138db693df283790
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947154"
---
# <a name="shape-json-to-maximize-query-performance"></a>Forma JSON para maximizar o desempenho da consulta 

Este artigo fornece orientação sobre como formatar JSON para maximizar a eficiência de suas consultas de Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Conheça as práticas recomendadas para formatar o JSON para atender às suas necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Melhores práticas
Pense em como você envia eventos para Time Series Insights. Ou seja, você sempre:

1. envie dados através da rede mais eficientemente possível.
1. Verifique se os dados estão armazenados de forma que você possa executar agregações adequadas para seu cenário.
1. Certifique-se de que você não atinja os limites de propriedade máxima de Time Series Insights de:
   - 600 Propriedades (colunas) para ambientes S1.
   - 800 Propriedades (colunas) para ambientes S2.

As diretrizes a seguir ajudam a garantir o melhor desempenho de consulta possível:

1. Não use propriedades dinâmicas, como uma ID de marca, como um nome de propriedade. Isso usa contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não for necessária, é melhor não enviá-la. Dessa forma, você evitará limitações de armazenamento.
1. Use [dados de referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos pela rede.
1. Compartilhe Propriedades de dimensão entre vários eventos para enviar dados pela rede com mais eficiência.
1. Não utilize o aninhamento de matriz profunda. O Time Series Insights dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. Time Series Insights mescla matrizes nas mensagens em vários eventos com pares de valor de propriedade.
1. Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados. Quando há várias medidas, enviá-las como valores em uma única propriedade minimiza a possibilidade de atingir o limite máximo da propriedade.

## <a name="example-overview"></a>Visão geral de exemplo

Os dois exemplos a seguir demonstram como enviar eventos para realçar as recomendações anteriores. Seguindo cada exemplo, você pode ver como as recomendações foram aplicadas.

Os exemplos se baseiam em um cenário em que vários dispositivos enviam medidas ou sinais. As medidas ou os sinais podem ser taxa de fluxo, pressão de óleo do motor, temperatura e umidade. No primeiro exemplo, há algumas medições em todos os dispositivos. O segundo exemplo tem muitos dispositivos, e cada dispositivo envia muitas medidas exclusivas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: Existem apenas algumas medições

> [!TIP]
> É recomendável que você envie cada medida ou sinal como uma propriedade ou coluna separada.

No exemplo a seguir, há uma única mensagem do Hub IoT do Azure em que a matriz externa contém uma seção compartilhada de valores de dimensão comuns. A matriz externa usa dados de referência para aumentar a eficiência da mensagem. Os dados de referência contêm metadados de dispositivo que não são alterados com todos os eventos, mas fornecem propriedades úteis para análise de dados. O envio em lote de valores de dimensão comuns e o uso de dados de referência economiza em bytes enviados pela conexão, o que torna a mensagem mais eficiente.

Exemplo de carga JSON:

```json
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

* Tabela de dados de referência que tem apropriedade de chave DeviceID:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* Time Series Insights tabela de eventos, após o nivelamento:

   | deviceId | messageId | deviceLocation | timestamp | série. Taxa de fluxo ft3/s | série. Psi de petróleo pressão do motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22,2 |

Observações sobre estas duas tabelas:

- A coluna DeviceID serve como o cabeçalho de coluna para os vários dispositivos em uma frota. Tornar o valor DeviceID seu próprio nome de propriedade limita o total de dispositivos a 595 (para ambientes S1) ou 795 (para ambientes S2) com as outras cinco colunas.
- Propriedades desnecessárias são evitadas, por exemplo, as informações de marca e modelo. Como as propriedades não serão consultadas no futuro, a eliminação delas permitirá uma melhor eficiência na rede e no armazenamento.
- Os dados de referência são usados para reduzir o número de bytes transferidos pela rede. Os dois atributos **MessageId** e **deviceLocation** são Unidos usando a propriedade de chaveDeviceID. Esses dados são associados aos dados de telemetria no momento da entrada e, em seguida, são armazenados em Time Series Insights para consulta.
- São usadas duas camadas de aninhamento, que é a quantidade máxima de aninhamento com suporte pelo Time Series Insights. É fundamental para evitar matrizes profundamente aninhadas.
- As medidas são enviadas como propriedades separadas dentro do mesmo objeto porque há poucas medidas. Aqui, **série. Taxa de fluxo psi** e **série. As ft3/s de pressão do óleo do motor** são colunas exclusivas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> Recomendamos que você envie medidas como tuplas "tipo", "unidade" e "valor".

Exemplo de carga JSON:

```json
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

* Tabela de dados de referência que tem as Propriedades de chave DeviceID e **Series. tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unidade |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_DE LINHA | UE | Taxa de fluxo | ft3/s |
   | FXXX | oilPressure | DADOS\_DE LINHA | UE | Pressão do óleo do motor | psi |
   | FYYY | pumpRate | DADOS\_DE LINHA | EUA | Taxa de fluxo | ft3/s |
   | FYYY | oilPressure | DADOS\_DE LINHA | EUA | Pressão do óleo do motor | psi |

* Time Series Insights tabela de eventos, após o nivelamento:

   | deviceId | series.tagId | messageId | deviceLocation | type | unidade | timestamp | série. valor |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_DE LINHA | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | DADOS\_DE LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | DADOS\_DE LINHA | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | DADOS\_DE LINHA | UE | Pressão do óleo do motor | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | DADOS\_DE LINHA | EUA | Taxa de fluxo | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | DADOS\_DE LINHA | EUA | Pressão do óleo do motor | psi | 2018-01-17T01:18:00Z | 22,2 |

Observações sobre estas duas tabelas:

- As colunas DeviceID e **Series. tagId** servem como cabeçalhos de coluna para os vários dispositivos e marcas em uma frota. O uso de cada um como seu próprio atributo limita a consulta ao total de dispositivos 594 (para ambientes S1) ou 794 (para ambientes S2), com as outras seis colunas.
- Propriedades desnecessárias foram evitadas, pelo motivo citado no primeiro exemplo.
- Os dados de referência são usados para reduzir o número de bytes transferidos pela rede apresentandoo DeviceID, que é usado para o par exclusivo de **MessageId** e **deviceLocation**. A série de chave composta **. tagId** é usada para o par exclusivo de **tipo** e **unidade**. A chave composta permite que o par DeviceID e **Series. tagId** seja usado para se referir a quatro valores: **MessageId, deviceLocation, Type** e **Unit**. Esses dados são associados aos dados de telemetria no momento da entrada. Em seguida, ele é armazenado em Time Series Insights para consulta.
- Duas camadas de aninhamento são usadas, pelo motivo citado no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Para uma propriedade com um grande número de valores possíveis, é melhor enviar como valores distintos dentro de uma única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

  - No primeiro exemplo, algumas propriedades têm vários valores, portanto, é apropriado tornar cada uma propriedade separada.
  - No segundo exemplo, as medidas não são especificadas como propriedades individuais. Em vez disso, eles são uma matriz de valores ou medidas em uma propriedade de série comum. A nova chave **tagId** é enviada, o que cria a nova coluna **Series. tagId** na tabela achatada. O novo **tipo** e a **unidade** de propriedades são criados usando dados de referência para que o limite de propriedade não seja atingido.

## <a name="next-steps"></a>Passos Seguintes

- Leia [Azure Time Series insights sintaxe de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) para saber mais sobre a sintaxe de consulta para a API REST de acesso a dados Time Series insights.
- Saiba [como formatar eventos](./time-series-insights-send-events.md).
