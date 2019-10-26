---
title: Regras baseadas em limite configuráveis no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para obter uma solução de alerta com regras baseadas em limite configuráveis no Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: f8fd21f411093e22b2b1dc5afd6da9cb26db6ff8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934265"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processar regras baseadas em limite configuráveis no Azure Stream Analytics
Este artigo descreve como usar dados de referência para obter uma solução de alerta que usa regras baseadas em limite configuráveis no Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Cenário: alertas com base em limites de regras ajustáveis
Talvez seja necessário produzir um alerta como saída quando os eventos transmitidos de entrada atingirem um determinado valor ou quando um valor agregado baseado nos eventos transmitidos de entrada exceder um determinado limite. É simples configurar uma consulta Stream Analytics que comparava o valor a um limite estático que é fixo e predeterminado. Um limite fixo pode ser embutido em código na sintaxe de consulta de streaming usando comparações numéricas simples (maior que, menor que e igualdade).

Em alguns casos, os valores de limite precisam ser facilmente configuráveis sem editar a sintaxe de consulta cada vez que um valor de limite é alterado. Em outros casos, você pode precisar de inúmeros dispositivos ou usuários processados pela mesma consulta com cada um deles com valores limites diferentes em cada tipo de dispositivo. 

Esse padrão pode ser usado para configurar limites dinamicamente, escolher seletivamente qual tipo de dispositivo o limite se aplica filtrando os dados de entrada e escolher seletivamente quais campos incluir na saída.

## <a name="recommended-design-pattern"></a>Padrão de design recomendado
Use uma entrada de dados de referência para um trabalho Stream Analytics como uma pesquisa dos limites de alerta:
- Armazene os valores de limite nos dados de referência, um valor por chave.
- Ingresse os eventos de entrada de dados de streaming nos dados de referência na coluna de chave.
- Use o valor com chave dos dados de referência como o valor de limite.

## <a name="example-data-and-query"></a>Dados de exemplo e consulta
No exemplo, os alertas são gerados quando a agregação de dados de streaming de dispositivos em uma janela de minuto e hora corresponde aos valores estipulados na regra fornecida como dados de referência.

Na consulta, para cada DeviceID e cada metricname sob a DeviceID, você pode configurar de 0 a 5 dimensões para agrupar por. Somente os eventos que têm os valores de filtro correspondentes são agrupados. Depois de agrupadas, agregações em janela de min, Max, AVG, são calculadas em uma janela de em cascata de 60 segundos. Os filtros nos valores agregados são então calculados de acordo com o limite configurado na referência, para gerar o evento de saída de alerta.

Por exemplo, suponha que haja um trabalho de Stream Analytics que tenha uma entrada de dados de referência denominada **regras e as** **métricas**de entrada de dados de streaming. 

## <a name="reference-data"></a>Dados de referência
Este exemplo de dados de referência mostra como uma regra baseada em limite pode ser representada. Um arquivo JSON mantém os dados de referência e são salvos no armazenamento de BLOBs do Azure, e esse contêiner de armazenamento de BLOBs é usado como uma entrada de dados de referência denominada **regras**. Você poderia substituir esse arquivo JSON e substituir a configuração da regra à medida que o tempo entrar, sem parar ou iniciar o trabalho de streaming.

- A regra de exemplo é usada para representar um alerta ajustável quando a CPU excede (a média é maior ou igual a) o valor `90` por cento. O campo `value` é configurável conforme necessário.
- Observe que a regra tem um campo **operador** , que é interpretado dinamicamente na sintaxe de consulta posteriormente em `AVGGREATEROREQUAL`. 
- A regra filtra os dados em uma determinada chave de dimensão `2` com o valor `C1`. Outros campos são uma cadeia de caracteres vazia, indicando não filtrar o fluxo de entrada pelos campos de evento. Você pode configurar regras de CPU adicionais para filtrar outros campos correspondentes, conforme necessário.
- Nem todas as colunas devem ser incluídas no evento de alerta de saída. Nesse caso, `includedDim` número de chave `2` é ativado `TRUE` para representar que o campo número 2 de dados de evento no fluxo será incluído nos eventos de saída de qualificação. Os outros campos não são incluídos na saída do alerta, mas a lista de campos pode ser ajustada.


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

## <a name="example-streaming-query"></a>Exemplo de consulta de streaming
Este exemplo Stream Analytics consulta une os dados de referência de **regras** do exemplo acima, a um fluxo de entrada de dados nomeados **métricas**.

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

## <a name="example-streaming-input-event-data"></a>Exemplo de dados de evento de entrada de streaming
Este exemplo de dados JSON representa os dados de entrada de **métricas** que são usados na consulta de streaming acima. 

- Três eventos de exemplo são listados dentro do período de 1 minuto, valor `T14:50`. 
- Todos os três têm o mesmo valor de `deviceId` `978648`.
- Os valores de métrica de CPU variam em cada evento, `98`, `95``80`, respectivamente. Somente os dois primeiros eventos de exemplo excedem a regra de alerta de CPU estabelecida na regra.
- O campo includeDim na regra de alerta foi o número de chave 2. O campo chave 2 correspondente nos eventos de exemplo é denominado `NodeName`. Os três eventos de exemplo têm valores `N024`, `N024`e `N014`, respectivamente. Na saída, você vê apenas o nó `N024`, pois esses são os únicos dados que correspondem aos critérios de alerta para alta CPU. `N014` não atende ao limite de CPU alto.
- A regra de alerta é configurada com um `filter` apenas no número de chave 2, que corresponde ao campo `cluster` nos eventos de exemplo. Todos os três eventos de exemplo têm valor `C1` e correspondem aos critérios de filtro.

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

## <a name="example-output"></a>Exemplo de saída
Este exemplo de dados JSON de saída mostra um único evento de alerta gerado com base na regra de limite de CPU definida nos dados de referência. O evento de saída contém o nome do alerta, bem como o agregado (Average, min, Max) dos campos considerados. Os dados de evento de saída incluem o campo número 2 `NodeName` valor `N024` devido à configuração da regra. (O JSON foi alterado para mostrar quebras de linha para facilitar a leitura.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```