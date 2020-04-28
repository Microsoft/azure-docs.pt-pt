---
title: Regras configuráveis baseadas em limiares no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para obter uma solução de alerta que tem regras baseadas em limiarconfiguráveis no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75369716"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processe regras baseadas em limiares configuráveis no Azure Stream Analytics
Este artigo descreve como usar dados de referência para obter uma solução de alerta que utiliza regras configuráveis baseadas em limiares no Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Cenário: Alerta com base em limiares de regras ajustáveis
Pode ser necessário produzir um alerta como saída quando os eventos streamed de entrada atingirem um determinado valor, ou quando um valor agregado baseado nos eventos streamed de entrada exceder um determinado limiar. É simples configurar uma consulta de Stream Analytics que comparou o valor a um limiar estático que é fixo e pré-determinado. Um limiar fixo pode ser codificado duramente na sintaxe de consulta de streaming utilizando comparações numéricas simples (maiores do que, menos do que, e igualdade).

Em alguns casos, os valores-limiar devem ser mais facilmente configuráveis sem editar a sintaxe de consulta cada vez que um valor limiar muda. Noutros casos, pode necessitar de inúmeros dispositivos ou utilizadores processados pela mesma consulta, com cada um deles a ter um valor de limiar diferente em cada tipo de dispositivo. 

Este padrão pode ser usado para configurar dinamicamente os limiares, escolher seletivamente que tipo de dispositivo o limiar aplica filtrando os dados de entrada e escolher seletivamente quais os campos a incluir na saída.

## <a name="recommended-design-pattern"></a>Padrão de design recomendado
Utilize uma entrada de dados de referência para um trabalho de Stream Analytics como uma análise dos limiares de alerta:
- Guarde os valores-limiar nos dados de referência, um valor por chave.
- Junte os eventos de entrada de dados de streaming aos dados de referência na coluna chave.
- Utilize o valor-chave dos dados de referência como valor-limiar.

## <a name="example-data-and-query"></a>Dados de exemplo e consulta
No exemplo, os alertas são gerados quando o agregado de dados que são transmitidos a partir de dispositivos numa janela de minutos corresponde aos valores estipulados na regra fornecida como dados de referência.

Na consulta, para cada dispositivoId, e cada nome métrico sob o dispositivoId, pode configurar de 0 a 5 dimensões para GROUP BY. Apenas os eventos com os valores de filtro correspondentes são agrupados. Uma vez agrupados, janelas de Min, Max, Avg, são calculados sobre uma janela de queda de 60 segundos. Os filtros dos valores agregados são então calculados de acordo com o limiar configurado na referência, para gerar o evento de saída de alerta.

Como exemplo, assuma que há um trabalho de Stream Analytics que tem uma entrada de dados de referência denominada **regras**, e transmissão de dados de entrada de dados chamadas **métricas**. 

## <a name="reference-data"></a>Dados de referência
Estes dados de referência mostram como uma regra baseada no limiar pode ser representada. Um ficheiro JSON contém os dados de referência e é guardado no armazenamento de blob Azure, e esse recipiente de armazenamento blob é usado como uma entrada de dados de referência denominada **regras**. Pode substituir este ficheiro JSON e substituir a configuração da regra à medida que o tempo passa, sem parar ou iniciar o trabalho de streaming.

- A regra do exemplo é usada para representar um alerta ajustável quando o `90` CPU excede (a média é maior ou igual a) o valor por cento. O `value` campo é configurável conforme necessário.
- Note que a regra tem um campo **de operador,** que é `AVGGREATEROREQUAL`dinamicamente interpretado na sintaxe de consulta mais tarde . 
- A regra filtra os dados numa `2` determinada `C1`chave de dimensão com valor . Outros campos são de cadeia vazia, indicando não filtrar o fluxo de entrada por esses campos de eventos. Pode criar regras adicionais de CPU para filtrar outros campos correspondentes, se necessário.
- Nem todas as colunas devem ser incluídas no evento de alerta de saída. Neste caso, `includedDim` o `2` número-chave é ligado `TRUE` para representar que o campo número 2 dos dados do evento no fluxo será incluído nos eventos de saída de qualificação. Os outros campos não estão incluídos na saída de alerta, mas a lista de campo pode ser ajustada.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Consulta de streaming de exemplo
Esta consulta de exemplo Stream Analytics junta os dados de referência das **regras** do exemplo acima, a um fluxo de entrada de dados **nomeados métricas**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Exemplo de dados de eventos de entrada de entrada
Este exemplo, os dados da JSON representam os dados de entrada de **métricas** que são utilizados na consulta de streaming acima. 

- Três eventos de exemplo são listados dentro `T14:50`do tempo de 1 minuto, valor . 
- Todos os três `deviceId` `978648`têm o mesmo valor.
- Os valores métricos do `98`CPU variam em cada evento, `95` `80` respectivamente. Apenas os dois primeiros exemplos de eventos excedem a regra de alerta da CPU estabelecida na regra.
- O campo incluídoDim na regra de alerta era a chave número 2. O campo chave 2 correspondente nos `NodeName`eventos de exemplo é nomeado . Os eventos de `N024` `N024`três `N014` exemplos têm valores, e respectivamente. Na saída, vê apenas `N024` o nó, pois são os únicos dados que correspondem aos critérios de alerta para cpu elevado. `N014`não satisfaz o limiar elevado da CPU.
- A regra de alerta `filter` é configurada com apenas no `cluster` número 2 da chave, que corresponde ao campo nos eventos da amostra. Os eventos de `C1` três exemplos têm valor e correspondem aos critérios do filtro.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Saída de exemplo
Estes dados de saída de exemplo JSON mostram que um único evento de alerta foi produzido com base na regra limiar do CPU definida nos dados de referência. O evento de saída contém o nome do alerta, bem como o agregado (médio, min, máx) dos campos considerados. Os dados do evento de `NodeName` `N024` saída incluem o valor da chave de campo número 2 devido à configuração da regra. (O JSON foi alterado para mostrar quebras de linha para a legibilidade.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
