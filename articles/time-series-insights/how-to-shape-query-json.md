---
title: Melhores práticas para formatação JSON em consultas do Azure Time Series Insights | Documentos da Microsoft
description: Saiba como melhorar sua eficiência de consulta do Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790072"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Como formatar o JSON para maximizar o desempenho de consulta 

Este artigo fornece orientações para a formatação do JSON, para maximizar a eficiência das consultas do Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Aprenda as melhores práticas para formatação JSON para satisfazer as necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Melhores práticas

É importante pensar sobre como enviar eventos para o Time Series Insights. Ou seja, deve sempre:

1. envie dados através da rede mais eficientemente possível.
1. Certifique-se de que os seus dados são armazenados de forma que lhe permite realizar agregações adequadas para o seu cenário.
1. Certifique-se de não atingiu limites de propriedade máximo do Time Series Insights de:
   - 600 propriedades (colunas) para ambientes de S1.
   - 800 propriedades (colunas) para ambientes de S2.

As seguintes orientações ajuda a garantir o melhor desempenho de consulta possíveis:

1. Não utilize propriedades dinâmicas, como um ID de tag como nome de propriedade, como contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não é necessária, é melhor não enviá-lo e evitar a limitações de armazenamento.
1. Uso [dados de referência](time-series-insights-add-reference-data-set.md), para evitar o envio de dados estáticos na rede.
1. Partilhar as propriedades de dimensão entre vários eventos, para enviar dados através da rede com mais eficiência.
1. Não utilize o aninhamento de matriz profunda. O Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. O Time Series Insights nivela as matrizes de nas mensagens, em vários eventos com pares de valor de propriedade.
1. Se apenas existirem algumas medidas para todas as ou a maioria dos eventos, é melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviar-lhe em separado reduz o número de eventos e pode fazer consultas com um melhor desempenho, já que menos eventos precisam ser processada. Quando há várias medidas, enviar-lhe como valores numa única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="example-overview"></a>Descrição geral de exemplo

Os seguintes dois exemplos demonstram o envio de eventos, para realçar as recomendações anteriores. Ao seguir cada exemplo, pode ver como as recomendações foram aplicadas.

Os exemplos são baseados num cenário em que vários dispositivos enviam medições ou sinais. Foi possível medidas ou sinais de taxa de fluxo, pressão do motor de petróleo, temperatura e humidade. No primeiro exemplo, há algumas medidas em todos os dispositivos. No segundo exemplo, existem vários dispositivos e envia cada muitas medições exclusivas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: existem apenas algumas medidas

> [!TIP]
> **Recomendação**: enviar cada medição/sinal como uma propriedade/coluna separada.

No exemplo a seguir, há uma única mensagem do IoT Hub, em que a matriz externa contém uma secção partilhada comuns de valores de dimensão. A matriz externa utiliza dados de referência para aumentar a eficiência da mensagem. Dados de referência contém os metadados do dispositivo, que não se altera com cada evento, mas fornece propriedades úteis para análise de dados. Os valores de dimensão comuns de criação de batches e empregando dados de referência, poupa no bytes enviados pela rede, tornando a mensagem mais eficiente.

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

* Tabela de dados de referência (é de propriedade de chave **deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINHA\_DADOS | UE |
   | FYYY | LINHA\_DADOS | EUA |

* Tabela de eventos de informações de série de tempo (depois de mesclar):

   | deviceId | messageId | deviceLocation | timestamp | série. Taxa de fluxo ft3/s | série. Psi de petróleo pressão do motor |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINHA\_DADOS | UE | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINHA\_DADOS | UE | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINHA\_DADOS | EUA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22,2 |

Acima:

- O **deviceId** coluna serve como o cabeçalho de coluna para os vários dispositivos numa frota. Tentativa de estabelecer o valor de deviceId seu próprio nome de propriedade, seria limitada total de dispositivos para 595 (ambientes de S1) ou 795 (ambientes de S2), com as outras colunas de cinco.

- Propriedades desnecessárias são evitadas, por exemplo, informações de marca e modelo, etc. Uma vez que eles não ser consultados no futuro, eliminá-las permite melhor rede e a eficiência de armazenamento.

- Dados de referência são usados para reduzir o número de bytes transferidos pela rede. Dois atributos, **messageId** e **deviceLocation**, são associadas através da propriedade de chave **deviceId**. Estes dados são associados com os dados de telemetria em tempo de entrada e, em seguida, armazenados no Time Series Insights para a consulta.

- São utilizadas duas camadas de aninhamento, que é a quantidade máxima de aninhamento suportado pelo Time Series Insights. É fundamental para evitar matrizes profundamente aninhadas.

- Medidas são enviadas como propriedades separadas dentro do mesmo objeto, uma vez que existem algumas medidas. Aqui, **série. Fluxo de psi taxa** e **série. O motor de pressão de petróleo ft3/s** são colunas exclusivas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> **Recomendação:** enviar medidas como "type", "unidade", "value" cadeias de identificação.

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

* Dados de referência (propriedades chave são **deviceId** e **series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unidade |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s |
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | psi |
   | FYYY | pumpRate | LINHA\_DADOS | EUA | Taxa de fluxo | ft3/s |
   | FYYY | oilPressure | LINHA\_DADOS | EUA | Motor petróleo pressão | psi |

* Tabela de eventos de informações de série de tempo (depois de mesclar):

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unidade | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINHA\_DADOS | UE | Taxa de fluxo | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINHA\_DADOS | UE | Motor petróleo pressão | Psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINHA\_DADOS | EUA | Taxa de fluxo | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINHA\_DADOS | EUA | Motor petróleo pressão | psi | 2018-01-17T01:18:00Z | 22,2 |

Acima:

- As colunas **deviceId** e **series.tagId** servir os cabeçalhos de coluna para os vários dispositivos e as etiquetas numa frota. Uso de cada um como seu próprio atributo seria limitada a consulta para 594 (ambientes de S1) ou 794 (ambientes de S2) totais de dispositivos com as outras seis colunas.

- propriedades desnecessárias foram evitadas, para o motivo pelo qual citado no primeiro exemplo.

- dados de referência são usados para reduzir o número de bytes transferidos pela rede, introduzindo **deviceId**, para um par exclusivo de **messageId** e **deviceLocation**. É utilizada uma chave composta, **series.tagId**, para o par exclusivo de **tipo** e **unidade**. A chave composta permite que o **deviceId** e **series.tagId** par a ser utilizado para fazer referência a quatro valores: **messageId, deviceLocation, escrever,** e **unidade** . Estes dados são associados com os dados de telemetria em tempo de entrada e, em seguida, armazenados no Time Series Insights para a consulta.

- duas camadas de aninhamento, são utilizadas para o motivo pelo qual citado no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Se tiver uma propriedade com um grande número de valores possíveis, é melhor enviar como valores distintos dentro de uma única coluna, em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

  - No primeiro exemplo, existem algumas propriedades que têm vários valores, para que seja apropriado para cada uma propriedade separada.
  - No entanto, no segundo exemplo, pode ver que as medidas não são especificadas como propriedades individuais, mas em vez disso, uma matriz de valores/medidas numa propriedade de série comum. É enviada uma nova chave, **tagId** , que cria uma nova coluna **series.tagId** na tabela simplificada. Novas propriedades são criadas, **tipo** e **unidade**, utilizando dados de referência, impedindo que o limite de propriedade de sendo visitado.

## <a name="next-steps"></a>Passos Seguintes

- Leia [sintaxe de consulta do Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) para saber mais sobre a sintaxe de consulta para a REST API de acesso os dados do Time Series Insights.

- Saiba mais [como eventos de forma](./time-series-insights-send-events.md).
