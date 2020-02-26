---
title: Registo de diagnósticos de desempenho de Insights Inteligentes
description: A Intelligent Insights fornece um registo de diagnóstico de problemas de desempenho da Base de Dados Azure SQL
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
ms.openlocfilehash: 8272867f5b6144b92dbffcf96cc539eb82f75801
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587356"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Utilize o registo de diagnósticos de diagnóstico de desempenho da Base de Dados Smart Insights Azure SQL

Esta página fornece informações sobre como usar o registo de diagnósticos de desempenho da Base de Dados Azure SQL gerado pela [Intelligent Insights,](sql-database-intelligent-insights.md)o seu formato, e os dados que contém para as suas necessidades de desenvolvimento personalizados. Pode enviar este registo de diagnóstico slog para [registos do Monitor Azure,](../azure-monitor/insights/azure-sql.md)Hubs de [Eventos Azure,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Armazenamento Azure,](sql-database-metrics-diag-logging.md#stream-diagnostic-telemetry-into-azure-storage)ou uma solução de terceiros para devOps personalizados alertando e reportando capacidades.

## <a name="log-header"></a>Cabeçalho de log

O registo de diagnósticos utiliza o formato padrão JSON para obter descobertas de Insights Inteligentes. A propriedade exata da categoria para aceder a um registo De Insights Inteligentes é o valor fixo "SQLInsights".

O cabeçalho do tronco é comum e consiste no carimbo de tempo (TimeGenerated) que mostra quando uma entrada foi criada. Também inclui um ID de recurso (ResourceId) que se refere à base de dados SQL particular a que a entrada se refere. A categoria (categoria), nível (Nível) e nome de operação (Nome de funcionamento) são propriedades fixas cujos valores não se alteram. Indicam que a entrada de registo é informativa e que provém de Insights Inteligentes (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Id e base de dados afetados

A propriedade de identificação de problemas (issueId_d) fornece uma forma de rastrear exclusivamente problemas de desempenho até ser resolvido. Vários registos de eventos no estado de reporte de registo da mesma emissão partilharão o mesmo ID de emissão.

Juntamente com o ID do problema, o registo de diagnósticoreporta os selos de hora de início (intervalStartTime_t) e final (intervalEndTme_t) do evento particular relacionado com um problema que é relatado no registo de diagnósticos.

A propriedade da piscina elástica (elasticPoolName_s) indica a que piscina elástica a base de dados com um problema pertence. Se a base de dados não faz parte de uma piscina elástica, esta propriedade não tem valor. A base de dados em que foi detetado um problema é divulgada na propriedade do nome da base de dados (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detetados

A secção seguinte do registo de desempenho da Intelligent Insights contém problemas de desempenho que foram detetados através da inteligência artificial incorporada. As deteções são divulgadas em propriedades dentro do registo de diagnósticos JSON. Estas deteções consistem na categoria de um problema, no impacto da questão, nas consultas afetadas e nas métricas. As propriedades de deteção podem conter múltiplos problemas de desempenho que foram detetados.

Os problemas de desempenho detetados são reportados com a seguinte estrutura de deteção de propriedades:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Os padrões de desempenho detetáveis e os detalhes que são lançados no registo de diagnóstico saem fornecidos na tabela seguinte.

### <a name="detection-category"></a>Categoria de deteção

A categoria (categoria) descreve a categoria de padrões de desempenho detetáveis. Consulte a tabela seguinte para todas as categorias possíveis de padrões de desempenho detetáveis. Para mais informações, consulte problemas de desempenho na base de [dados de Troubleshoot com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

Dependendo do problema de desempenho detetado, os detalhes obtidos no ficheiro de registo de diagnóstico diferem em conformidade.

| Padrões de desempenho detetáveis | Detalhes divulgados |
| :------------------- | ------------------- |
| Atingir limites de recursos | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentagem de consumo de recursos</li> |
| Aumento da carga de trabalho | <li>Número de consultas cuja execução aumentou</li><li>Consultas de consultas com maior contribuição para o aumento da carga de trabalho</li> |
| Pressão da Memória | <li>Escriturário de memória</li> |
| Bloqueio | <li>Hashes de consulta afetada</li><li>Bloqueio de hashes de consulta</li> |
| MAXDOP aumentado | <li>Hashes de consulta</li><li>Tempos de espera CXP</li><li>Tempos de espera</li> |
| Contenção pagelatch | <li>Hashes de consultas de consulta causando contenção</li> |
| Índice em falta | <li>Hashes de consulta</li> |
| Nova Consulta | <li>Hash de consulta das novas consultas</li> |
| Estatística de espera incomum | <li>Tipos de espera incomuns</li><li>Hashes de consulta</li><li>Tempos de espera de consulta</li> |
| Contenção TempDB | <li>Hashes de consultas de consulta causando contenção</li><li>Atribuição de consulta à base de dados geral pagelatch tempo de espera [%]</li> |
| Escassez de DTU da piscina elástica | <li>Conjunto elástico</li><li>Base de dados de topo consumista dTU</li><li>Por cento da piscina DTU usada pelo consumidor de topo</li> |
| Plano regressão | <li>Hashes de consulta</li><li>Boas iDs de plano</li><li>IDs de mau plano</li> |
| Alteração do valor da configuração com código de base de dados | <li>Alterações de configuração com código de base de dados em comparação com os valores predefinidos</li> |
| Cliente Lento | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Desvalorização do nível de preços | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

A propriedade de impacto (impacto) descreve o quanto um comportamento detetado contribuiu para o problema que uma base de dados está a ter. Os impactos variam de 1 a 3, com 3 como a contribuição mais elevada, 2 como moderada, e 1 como a contribuição mais baixa. O valor de impacto pode ser usado como uma entrada para automatização de alerta personalizado, dependendo das suas necessidades específicas. As consultas imobiliárias impactadas (QueryHashes) fornecem uma lista das hashes de consulta que foram afetadas por uma deteção particular.

### <a name="impacted-queries"></a>Consultas impactadas

A secção seguinte do registo Smart Insights fornece informações sobre consultas específicas que foram afetadas pelos problemas de desempenho detetados. Esta informação é divulgada como uma variedade de objetos incorporados na propriedade impact_s. A propriedade de impacto é constituída por entidades e métricas. As entidades referem-se a uma consulta particular (Tipo: Consulta). O hash de consulta única é divulgado sob a propriedade de valor (Valor). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor, que indicam um problema de desempenho detetado.

No seguinte exemplo de registo, foi detetada a consulta com o haxixe 0x9102EXZ4 para ter uma duração acrescida de execução (Métrica: DuraçõesIncreaseSeconds). O valor de 110 segundos indica que esta consulta em particular demorou 110 segundos a ser executada. Como várias consultas podem ser detetadas, esta secção de registo em particular pode incluir múltiplas consultas.

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

A unidade de medição para cada métrica reportada é fornecida sob a propriedade métrica (métrica) com os valores possíveis de segundos, número e percentagem. O valor de uma métrica medida é reportado no valor (valor) da propriedade.

A propriedade DurationIncreaseSeconds fornece a unidade de medição em segundos. A unidade de medição criticalErrorCount é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de análise e melhoria de causas

A última parte do registo de desempenho da Intelligent Insights diz respeito à análise automatizada da causa da raiz do problema de degradação do desempenho identificado. A informação aparece na verbiagem amiga do homem na propriedade de análise de causas de raiz (rootCauseAnalysis_s). As recomendações de melhoria são incluídas no registo sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Pode utilizar o registo de desempenho do Intelligent Insights com [registos do Monitor Azure]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para devOps personalizados, alertando e reportando capacidades.

## <a name="next-steps"></a>Passos seguintes
- Conheça os conceitos [De Insights Inteligentes.](sql-database-intelligent-insights.md)
- Saiba como resolver problemas de desempenho da Base de [Dados Azure SQL com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).
- Saiba como monitorizar a Base de [Dados Azure SQL utilizando o Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [recolher e consumir dados de registo dos seus recursos Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)



