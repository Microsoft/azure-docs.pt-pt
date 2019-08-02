---
title: Log de diagnóstico de desempenho do Intelligent Insights-banco de dados SQL do Azure | Microsoft Docs
description: Intelligent Insights fornece um log de diagnóstico de problemas de desempenho do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: 8180fc4db10019a3183af40cf21d9d92b0102201
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567895"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Usar o Intelligent Insights log de diagnóstico de desempenho do banco de dados SQL do Azure

Esta página fornece informações sobre como usar o log de diagnóstico de desempenho do banco de dados SQL do Azure gerado por [Intelligent insights](sql-database-intelligent-insights.md), seu formato e os dados que ele contém para suas necessidades de desenvolvimento personalizadas. Você pode enviar esse log de diagnóstico para [Azure monitor logs](../azure-monitor/insights/azure-sql.md), [hubs de eventos do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage)ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

## <a name="log-header"></a>Cabeçalho do log

O log de diagnóstico usa o formato padrão JSON para produzir resultados de Intelligent Insights de saída. A propriedade de categoria exata para acessar um log de Intelligent Insights é o valor fixo "sqlsights".

O cabeçalho do log é comum e consiste no carimbo de data/hora (TimeGenerated) que mostra quando uma entrada foi criada. Ele também inclui uma ID de recurso (ResourceId) que se refere ao banco de dados SQL específico ao qual a entrada está relacionada. A categoria (categoria), nível (nível) e nome da operação (OperationName) são propriedades fixas cujos valores não são alterados. Eles indicam que a entrada de log é informativa e que vem de Intelligent Insights (sqlsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID do problema e banco de dados afetados

A propriedade de identificação do problema (issueId_d) fornece uma maneira de controlar com exclusividade os problemas de desempenho até que sejam resolvidos. Vários registros de eventos no status do relatório de log do mesmo problema compartilharão a mesma ID de problema.

Junto com a ID do problema, o log de diagnóstico relata os carimbos de data/hora de início (intervalStartTime_t) e término (intervalEndTme_t) do evento específico relacionado a um problema relatado no log de diagnóstico.

A propriedade pool elástico (elasticPoolName_s) indica a qual pool elástico o banco de dados com um problema pertence. Se o banco de dados não fizer parte de um pool elástico, essa propriedade não terá nenhum valor. O banco de dados no qual um problema foi detectado é divulgado na propriedade nome do banco de dados (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detectados

A próxima seção do log de desempenho do Intelligent Insights contém problemas de desempenho que foram detectados por meio de inteligência artificial interna. As detecções são divulgadas nas propriedades no log de diagnósticos JSON. Essas detecções consistem na categoria de um problema, no impacto do problema, nas consultas afetadas e nas métricas. As propriedades de detecções podem conter vários problemas de desempenho detectados.

Problemas de desempenho detectados são relatados com a seguinte estrutura de propriedade de detecções:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Os padrões de desempenho detectáveis e os detalhes que são enviados para o log de diagnóstico são fornecidos na tabela a seguir.

### <a name="detection-category"></a>Categoria de detecção

A propriedade categoria (categoria) descreve a categoria de padrões de desempenho detectáveis. Consulte a tabela a seguir para todas as categorias possíveis de padrões de desempenho detectáveis. Para obter mais informações, consulte [solucionar problemas de desempenho do banco de dados com o Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Dependendo do problema de desempenho detectado, os detalhes enviados no arquivo de log de diagnósticos diferem de acordo.

| Padrões de desempenho detectáveis | Detalhes enviados |
| :------------------- | ------------------- |
| Atingir limites de recursos | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Porcentagem de consumo de recursos</li> |
| Aumento da carga de trabalho | <li>Número de consultas cuja execução aumentou</li><li>Hashes de consulta de consultas com a maior contribuição para o aumento da carga de trabalho</li> |
| Pressão de memória | <li>Administrador de memória</li> |
| Bloqueio | <li>Hashes de consulta afetados</li><li>Bloqueando hashes de consulta</li> |
| Maior MAXDOP | <li>Hashes de consulta</li><li>CXP tempos de espera</li><li>Tempos de espera</li> |
| Contenção de Pagelatch | <li>Hashes de consulta de consultas causando contenção</li> |
| Índice ausente | <li>Hashes de consulta</li> |
| Nova Consulta | <li>Hash de consulta das novas consultas</li> |
| Estatística de espera incomum | <li>Tipos de espera incomum</li><li>Hashes de consulta</li><li>Tempos de espera da consulta</li> |
| Contenção de TempDB | <li>Hashes de consulta de consultas causando contenção</li><li>Atribuição de consulta ao tempo de espera de contenção de pagelatch do banco de dados geral [%]</li> |
| Escassez de DTU do pool elástico | <li>Conjunto elástico</li><li>Banco de dados de consumo de DTU principal</li><li>Porcentagem de DTU de pool usada pelo consumidor superior</li> |
| Regressão do plano | <li>Hashes de consulta</li><li>Boas IDs de plano</li><li>IDs de plano inválidos</li> |
| Alteração do valor de configuração no escopo do banco de dados | <li>Alterações de configuração no escopo do banco de dados em comparação com os valores padrão</li> |
| Cliente lento | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Downgrade de tipo de preço | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

A propriedade impacto (impacto) descreve o quanto um comportamento detectado contribuiu para o problema que um banco de dados está tendo. O impacto varia de 1 a 3, com 3 como a contribuição mais alta, 2 como moderada e 1 como a contribuição mais baixa. O valor de impacto pode ser usado como uma entrada para automação de alertas personalizados, dependendo de suas necessidades específicas. As consultas de propriedade afetadas (QueryHashes) fornecem uma lista dos hashes de consulta que foram afetados por uma determinada detecção.

### <a name="impacted-queries"></a>Consultas afetadas

A próxima seção do log de Intelligent Insights fornece informações sobre as consultas específicas que foram afetadas pelos problemas de desempenho detectados. Essas informações são divulgadas como uma matriz de objetos inseridos na propriedade impact_s. A propriedade Impact consiste em entidades e métricas. As entidades referem-se a uma consulta específica (tipo: Consulta). O hash de consulta exclusivo é divulgado na propriedade valor (valor). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor, que indicam um problema de desempenho detectado.

No exemplo de log a seguir, a consulta com o hash 0x9102EXZ4 foi detectada para ter uma duração maior de execução (métrica: DurationIncreaseSeconds). O valor de 110 segundos indica que essa consulta específica levou 110 segundos mais para ser executada. Como várias consultas podem ser detectadas, essa seção de log específica pode incluir várias entradas de consulta.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Métricas

A unidade de medida para cada métrica relatada é fornecida sob a propriedade métrica (métrica) com os valores possíveis de segundos, número e porcentagem. O valor de uma métrica medida é relatado na propriedade valor (valor).

A propriedade DurationIncreaseSeconds fornece a unidade de medida em segundos. A unidade de medida CriticalErrorCount é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Análise da causa raiz e recomendações de aperfeiçoamento

A última parte do log de desempenho Intelligent Insights pertence à análise de causa raiz automatizada do problema de degradação de desempenho identificado. As informações são exibidas em argumentação amigáveis para o homem na propriedade rootCauseAnalysis_s (análise da causa raiz). As recomendações de aprimoramento são incluídas no log sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Você pode usar o log de desempenho Intelligent Insights com [Azure monitor logs]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para recursos personalizados de alertas e relatórios de DevOps.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre os conceitos de [Intelligent insights](sql-database-intelligent-insights.md) .
- Saiba como [solucionar problemas de desempenho do banco de dados SQL do Azure com o Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Saiba como [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [coletar e consumir dados de log de seus recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



