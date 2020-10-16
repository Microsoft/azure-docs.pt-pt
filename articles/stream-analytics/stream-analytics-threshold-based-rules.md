---
title: Regras configuráveis baseadas em limiares no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para alcançar uma solução de alerta que tem regras baseadas em limiares configuráveis no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 215835bf7f1e6676adba6541da70dcb86fc3500c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039046"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Regras baseadas em limiar de processos configuráveis no Azure Stream Analytics
Este artigo descreve como usar dados de referência para alcançar uma solução de alerta que utiliza regras baseadas em limiares configuráveis no Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Cenário: Alerta com base nos limiares de regras ajustáveis
Poderá ter de produzir um alerta como saída quando os eventos transmitidos por entrada atingirem um determinado valor, ou quando um valor agregado baseado nos eventos transmitidos de entrada excede um determinado limiar. É simples configurar uma consulta stream Analytics que comparou o valor a um limiar estático que é fixo e pré-determinado. Um limiar fixo pode ser codificado duramente na sintaxe de consulta de streaming utilizando comparações numéricas simples (maiores do que, menos do que, e igualdade).

Em alguns casos, os valores limiar devem ser mais facilmente configuráveis sem editar a sintaxe de consulta cada vez que um valor limiar muda. Noutros casos, poderá necessitar de inúmeros dispositivos ou utilizadores processados pela mesma consulta, com cada um deles a ter um limiar diferente em cada tipo de dispositivo. 

Este padrão pode ser usado para configurar dinamicamente os limiares, escolher seletivamente que tipo de dispositivo o limiar aplica através da filtragem dos dados de entrada e escolher seletivamente quais os campos a incluir na saída.

## <a name="recommended-design-pattern"></a>Padrão de design recomendado
Utilize uma entrada de dados de referência para um trabalho do Stream Analytics como uma análise dos limiares de alerta:
- Armazenar os valores-limiar nos dados de referência, um valor por chave.
- Junte os eventos de entrada de dados de streaming aos dados de referência na coluna-chave.
- Utilize o valor de teclas a partir dos dados de referência como valor limiar.

## <a name="example-data-and-query"></a>Exemplo de dados e consulta
No exemplo, os alertas são gerados quando o agregado de dados que são transmitidos a partir de dispositivos numa janela de minutos corresponde aos valores estipulados na regra fornecida como dados de referência.

Na consulta, para cada dispositivoId, e cada nome métrico sob o dispositivoId, pode configurar de 0 a 5 dimensões para GROUP BY. Apenas os eventos com os valores do filtro correspondentes são agrupados. Uma vez agrupados, os agregados de Min, Max, Avg, são calculados ao longo de uma janela de 60 segundos. Os filtros nos valores agregados são então calculados de acordo com o limiar configurado na referência, para gerar o evento de saída de alerta.

Como exemplo, assuma que existe um trabalho de Stream Analytics que tem uma entrada de dados de referência denominada **regras**, e streaming de **dados denominados métricas**. 

## <a name="reference-data"></a>Dados de referência
Este exemplo de dados de referência mostra como uma regra baseada em limiares poderia ser representada. Um ficheiro JSON contém os dados de referência e é guardado no armazenamento de bolhas Azure, e esse recipiente de armazenamento de bolhas é usado como uma entrada de dados de referência denominada **regras**. Pode substituir este ficheiro JSON e substituir a configuração da regra à medida que o tempo passa, sem parar ou iniciar o trabalho de streaming.

- A regra do exemplo é usada para representar um alerta ajustável quando a CPU excede (a média é maior ou igual a) o valor `90` por cento. O `value` campo é configurável conforme necessário.
- Note que a regra tem um campo **de operador,** que é interpretado dinamicamente na sintaxe de consulta mais tarde `AVGGREATEROREQUAL` . 
- A regra filtra os dados numa determinada tecla de dimensão `2` com valor `C1` . Outros campos são de corda vazia, indicando para não filtrar o fluxo de entrada por esses campos de evento. Pode criar regras adicionais de CPU para filtrar outros campos correspondentes, se necessário.
- Nem todas as colunas devem ser incluídas no evento de alerta de saída. Neste caso, `includedDim` o número-chave `2` é ligado para representar que o número de campo `TRUE` 2 dos dados do evento no stream será incluído nos eventos de saída qualificados. Os outros campos não estão incluídos na saída de alerta, mas a lista de campo pode ser ajustada.


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
Este exemplo A consulta stream Analytics une as **regras** de referência dados do exemplo acima, a um fluxo de entrada de **métricas nomeadas**.

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

## <a name="example-streaming-input-event-data"></a>Exemplo de streaming de dados de eventos de entrada
Este exemplo de dados JSON representa os dados de entrada **de métricas** que são usados na consulta de streaming acima. 

- Três exemplos de eventos são listados dentro do intervalo de 1 minuto, valor `T14:50` . 
- Os três têm o mesmo `deviceId` `978648` valor.
- Os valores métricos da CPU variam em cada evento, `98` `95` `80` respectivamente. Apenas os dois primeiros exemplos de eventos excedem a regra de alerta do CPU estabelecida na regra.
- O campo incluindo oDim na regra de alerta era a chave número 2. O campo de tecla 2 correspondente nos eventos de exemplo é nomeado `NodeName` . Os três exemplos de eventos têm valores `N024` `N024` , `N014` e, respectivamente. Na saída, você vê apenas o nó `N024` como que são os únicos dados que correspondem aos critérios de alerta para CPU elevado. `N014` não satisfaz o limiar elevado da CPU.
- A regra de alerta é configurada com um `filter` apenas na chave número 2, que corresponde ao `cluster` campo nos eventos da amostra. Os três exemplos de eventos têm valor `C1` e correspondem aos critérios do filtro.

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
Este exemplo de saída Os dados do JSON mostram que foi produzido um único evento de alerta com base na regra do limiar da CPU definida nos dados de referência. O evento de saída contém o nome do alerta, bem como o agregado (médio, min, máx) dos campos considerados. Os dados do evento de saída incluem o valor da chave de campo número 2 `NodeName` `N024` devido à configuração da regra. (O JSON foi alterado para mostrar quebras de linha para a legibilidade.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
