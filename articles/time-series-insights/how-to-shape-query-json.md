---
title: Melhores práticas para formatação JSON em consultas do Azure Time Series Insights | Documentos da Microsoft
description: Saiba como melhorar sua eficiência de consulta do Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244465"
---
# <a name="shape-json-to-maximize-query-performance"></a>Forma JSON para maximizar o desempenho de consulta 

Este artigo fornece orientações sobre como a forma JSON para maximizar a eficiência das consultas do Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Aprenda as melhores práticas para formatação JSON para satisfazer as necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Melhores práticas
Pense em como os eventos são enviados para o Time Series Insights. Ou seja, sempre:

1. envie dados através da rede mais eficientemente possível.
1. Certifique-se de que os dados são armazenados de uma forma para que possam realizar agregações adequadas para o seu cenário.
1. Certifique-se de que não a atingir os limites de propriedade máximo de Time Series Insights de:
   - 600 propriedades (colunas) para ambientes de S1.
   - 800 propriedades (colunas) para ambientes de S2.

As seguintes orientações ajudam a garantir o melhor desempenho de consulta possíveis:

1. Não utilize propriedades dinâmicas, como um ID de etiqueta, como um nome de propriedade. Esta utilização contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não é necessária, é melhor não enviá-lo. Dessa maneira é evitar as limitações de armazenamento.
1. Uso [dados de referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos na rede.
1. Partilhar as propriedades de dimensão entre vários eventos para enviar dados através da rede com mais eficiência.
1. Não utilize o aninhamento de matriz profunda. O Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. O Time Series Insights nivela as matrizes de nas mensagens em vários eventos com pares de valor de propriedade.
1. Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviar-lhe em separado reduz o número de eventos e pode melhorar o desempenho de consulta porque é necessário processar menos eventos. Quando há várias medidas, enviar-lhe como valores numa única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="example-overview"></a>Descrição geral de exemplo

Os dois exemplos seguintes demonstram como enviar eventos para realçar as recomendações anteriores. Ao seguir cada exemplo, pode ver como as recomendações foram aplicadas.

Os exemplos são baseados num cenário em que vários dispositivos enviam medições ou sinais. Medidas ou sinais podem ser a taxa de fluxo, pressão do motor de petróleo, temperatura e humidade. No primeiro exemplo, há algumas medidas em todos os dispositivos. O segundo exemplo tem muitos dispositivos, e cada dispositivo envia muitas medições exclusivas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: Existem apenas algumas medidas

> [!TIP]
> Recomendamos que envie cada medida ou o sinal como uma propriedade separada ou uma coluna.

No exemplo a seguir, há uma única mensagem do IoT Hub do Azure em que a matriz externa contém uma secção partilhada comuns de valores de dimensão. A matriz externa utiliza dados de referência para aumentar a eficiência da mensagem. Dados de referência contém metadados de dispositivo que não é alterada com cada evento, mas ela fornece propriedades úteis para análise de dados. Criação de batches de valores de dimensão comuns e empregando dados salva em bytes enviados durante a transmissão de referência, que torna a mensagem mais eficientes.

Payload JSON de exemplo:

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

* Tabela de dados de referência que tem a propriedade da chave **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINHA\_DADOS | UE |
   | FYYY | LINHA\_DADOS | EUA |

* Time Series Insights eventos tabela, depois de mesclar:

   | deviceId | messageId | deviceLocation | timestamp | série. Taxa de fluxo ft3/s | série. Psi de petróleo pressão do motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINHA\_DADOS | UE | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINHA\_DADOS | UE | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINHA\_DADOS | EUA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22,2 |

Notas sobre essas duas tabelas:

- O **deviceId** coluna serve como o cabeçalho de coluna para os vários dispositivos numa frota. Colocando o valor de deviceId seu próprio nome de propriedade limita o total de dispositivos para 595 (para S1 ambientes) ou 795 (para ambientes de S2) com as outras colunas de cinco.
- Propriedades desnecessárias são evitadas, para obter informações de exemplo, a marca e modelo. Uma vez que as propriedades não é possível consultar no futuro, eliminá-las permite melhor rede e a eficiência de armazenamento.
- Dados de referência são usados para reduzir o número de bytes transferidos pela rede. Os dois atributos **messageId** e **deviceLocation** são associadas ao utilizar a propriedade da chave **deviceId**. Estes dados são associados com os dados de telemetria em tempo de entrada e, em seguida, são armazenados no Time Series Insights para a consulta.
- São utilizadas duas camadas de aninhamento, que é a quantidade máxima de aninhamento suportado pelo Time Series Insights. É fundamental para evitar matrizes profundamente aninhadas.
- Medidas são enviadas como propriedades separadas dentro do mesmo objeto, uma vez que existem algumas medidas. Aqui, **série. Fluxo de psi taxa** e **série. O motor de pressão de petróleo ft3/s** são colunas exclusivas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> Recomendamos que envia medidas como "de tipo," "unidade" e "valor" cadeias de identificação.

Payload JSON de exemplo:

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

* Tabela de dados de referência que tem as propriedades chave **deviceId** e **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | Unidade |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s |
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | psi |
   | FYYY | pumpRate | LINHA\_DADOS | EUA | Taxa de fluxo | ft3/s |
   | FYYY | oilPressure | LINHA\_DADOS | EUA | Motor petróleo pressão | psi |

* Time Series Insights eventos tabela, depois de mesclar:

   | deviceId | series.tagId | messageId | deviceLocation | type | Unidade | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINHA\_DADOS | EUA | Taxa de fluxo | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINHA\_DADOS | EUA | Motor petróleo pressão | psi | 2018-01-17T01:18:00Z | 22,2 |

Notas sobre essas duas tabelas:

- As colunas **deviceId** e **series.tagId** servir os cabeçalhos de coluna para os vários dispositivos e as etiquetas numa frota. Uso de cada como seu próprio atributo limita a consulta para 594 (para S1 ambientes) ou 794 (para ambientes de S2) total de dispositivos com as outras seis colunas.
- propriedades desnecessárias foram evitadas, para o motivo pelo qual citado no primeiro exemplo.
- Dados de referência são usados para reduzir o número de bytes transferidos pela rede, introduzindo **deviceId**, que é utilizado para o par exclusivo de **messageId** e **deviceLocation**. A chave composta **series.tagId** é utilizado para o par exclusivo de **tipo** e **unidade**. A chave composta permite que o **deviceId** e **series.tagId** par a ser utilizado para fazer referência a quatro valores: **messageId, deviceLocation, escrever,** e **unidade**. Estes dados estão associados com os dados de telemetria em tempo de entrada. Em seguida, é armazenada no Time Series Insights para a consulta.
- duas camadas de aninhamento, são utilizadas para o motivo pelo qual citado no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Para uma propriedade com um grande número de valores possíveis, é melhor enviar como valores distintos numa única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

  - No primeiro exemplo, algumas propriedades tem vários valores, para que seja apropriado para cada uma propriedade separada.
  - No segundo exemplo, as medidas não são especificadas como propriedades individuais. Em vez disso, eles são uma matriz de valores ou medidas numa propriedade de série comum. A nova chave **tagId** é enviado, que cria a nova coluna **series.tagId** na tabela simplificada. As novas propriedades **tipo** e **unidade** são criadas com dados de referência para que não é atingido o limite de propriedade.

## <a name="next-steps"></a>Passos Seguintes

- Leia [sintaxe de consulta do Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) para saber mais sobre a sintaxe de consulta para a REST API de acesso os dados do Time Series Insights.
- Saiba mais [como eventos de forma](./time-series-insights-send-events.md).
