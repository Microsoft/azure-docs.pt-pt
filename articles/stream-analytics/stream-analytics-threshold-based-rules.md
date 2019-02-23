---
title: Limiar de processo configurável com base em regras no Azure Stream Analytics
description: Este artigo descreve como utilizar os dados de referência para obter uma solução de alertas que tem regras de limiar configurável com base no Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731196"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processar regras baseadas no limiar no Azure Stream Analytics configuráveis
Este artigo descreve como utilizar os dados de referência para obter uma solução de alertas que utiliza regras configuráveis baseados no limiar no Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Cenário: Alertas com base nos limiares de regra ajustável
Terá de gerar um alerta como saída quando eventos transmitidos em fluxo recebidos atingiu um determinado valor, ou quando um valor agregado com base em eventos de transmissão em fluxo contínuo entrada excede um determinado limiar. Ele é fácil de configurar uma consulta do Stream Analytics que em comparação com o valor para um limite de estático que é fixo e predeterminado. Um limite fixo pode ser codificada para a sintaxe de consulta de transmissão em fluxo com comparações numéricas simples (superior, inferior e igualdade).

Em alguns casos, os valores de limiar tem de ser mais facilmente configuráveis sem editar a sintaxe de consulta sempre que um valor de limiar é alterado. Em outros casos, poderá ter vários dispositivos ou utilizadores processados pela mesma consulta, com cada uma delas tendo um valores limite diferentes em cada tipo de dispositivo. 

Este padrão pode ser usado para configurar limiares de forma dinâmica, seletivamente, escolha o tipo de dispositivo, o limite se aplica ao filtrar os dados de entrada e seletivamente escolher quais campos serão incluídos na saída.

## <a name="recommended-design-pattern"></a>Padrão de design recomendado
Utilize uma entrada de dados de referência para uma tarefa do Stream Analytics como uma pesquisa dos limites de alerta:
- Store os valores de limiar nos dados de referência, um valor por chave.
- Junte-se os eventos de entrada de dados de transmissão em fluxo para os dados de referência na coluna chave.
- Utilize o valor com chave dos dados de referência como o valor de limiar.

## <a name="example-data-and-query"></a>Dados de exemplo e consulta
No exemplo, os alertas são gerados quando a agregação de dados transmitidos em fluxo de dispositivos numa janela de minutos longa correspondem aos valores estipulados na regra fornecidos como dados de referência.

Na consulta, para cada deviceId e cada metricName sob o ID do dispositivo, pode configurar de 0 a 5 dimensões para GROUP BY. Apenas os eventos com os valores de filtro correspondente são agrupados. Depois de agrupados, agregados em janelas do Min, Máx, média, são calculados numa janela em cascata de 60 segundos. Filtros de valores agregados, em seguida, são calculados de acordo com o limiar configurado de referência, para gerar o evento de saída de alerta.

Por exemplo, suponha que existe uma tarefa de Stream Analytics tem uma entrada de dados de referência com o nome **regras**e com o nome de entrada de dados de transmissão em fluxo **métricas**. 

## <a name="reference-data"></a>Dados de referência
Estes dados de referência de exemplo mostram como uma regra baseada em limiar poderia ser representada. Um ficheiro JSON contém os dados de referência e é guardado no armazenamento de Blobs do Azure, e esse contentor de armazenamento de BLOBs é utilizado como uma entrada de dados de referência com o nome **regras**. Poderia substituir este ficheiro JSON e substituir a configuração da regra, conforme o tempo passa, sem interromper ou a iniciar a tarefa de transmissão em fluxo.

- A regra de exemplo é utilizada para representar um alerta ajustável quando excede de CPU (média é superior ou igual a) o valor `90` por cento. O `value` campo pode ser configurado conforme necessário.
- Tenha em atenção a regra tem um **operador** campo, o que é interpretado dinamicamente a sintaxe de consulta mais tarde `AVGGREATEROREQUAL`. 
- A regra filtra os dados numa determinada chave de dimensão `2` com o valor `C1`. Outros campos são uma cadeia vazia, que indica que não para filtrar o fluxo de entrada por esses campos de evento. Poderia configurar regras de CPU adicionais para filtrar os outros campos conforme necessário.
- Nem todas as colunas são incluídos no evento de alerta de saída. Neste caso, `includedDim` número de chaves `2` está ativada `TRUE` para representar que número 2 do campo de dados de eventos no fluxo será incluído nos eventos de saída elegíveis. Os outros campos não estão incluídos na saída do alerta, mas pode ser ajustada a lista de campos.


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

## <a name="example-streaming-query"></a>Consulta de transmissão em fluxo de exemplo
Esta consulta do Stream Analytics de exemplo é associado a **regras** dados de referência do exemplo acima, para um fluxo de entrada de dados com o nome **métricas**.

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

## <a name="example-streaming-input-event-data"></a>Dados de evento de entrada de transmissão em fluxo exemplo
Neste exemplo, dados JSON representa a **métricas** dados que são utilizados na consulta acima de transmissão em fluxo de entrada. 

- Três eventos de exemplo estão listados dentro do período de tempo de 1 minuto, valor `T14:50`. 
- Todos os três têm a mesma `deviceId` valor `978648`.
- Os valores de métrica de CPU variam em cada evento `98`, `95`, `80` , respetivamente. Apenas os primeiros eventos de duas exemplo excederem a regra de alerta de CPU estabelecida na regra.
- O campo de includeDim na regra de alerta foi número chave 2. O campo de chave 2 correspondente nos eventos de exemplo é chamado `NodeName`. Os eventos de três exemplo têm valores `N024`, `N024`, e `N014` , respetivamente. Na saída, verá apenas o nó `N024` como isto é os únicos dados que corresponda os critérios de alerta para elevada da CPU. `N014` não cumpre o limiar de CPU elevado.
- A regra de alerta está configurada com um `filter` apenas nos principais número 2, que corresponde à `cluster` campo em eventos de exemplo. Os eventos de três exemplo todos têm valor `C1` e corresponde aos critérios de filtro.

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
Neste exemplo de saída JSON mostra de dados foi produzido um único evento de alerta com base na regra de limite de CPU definida nos dados de referência. O evento de saída contém o nome do alerta, bem como a agregados (média, Mín, Máx.) dos campos considerados. Os dados de eventos de saída incluem o número do campo chave 2 `NodeName` valor `N024` devido à configuração da regra. (O JSON foi alterado para mostrar as quebras de linha para facilitar a leitura.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```