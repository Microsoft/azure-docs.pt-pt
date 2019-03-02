---
title: Registo de inteligente diagnóstico de desempenho do Insights - base de dados SQL do Azure | Documentos da Microsoft
description: Informações inteligentes fornece um registo de diagnóstico dos problemas de desempenho da base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 264d4cfc6b09813f34501a0e51d3100f4d2bce78
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217041"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Utilizar o registo de diagnóstico de desempenho do Intelligent Insights SQL Database do Azure

Esta página fornece informações sobre como utilizar o log de diagnóstico de desempenho de base de dados do Azure SQL gerado pelo [informações inteligentes](sql-database-intelligent-insights.md), seu formato e os dados nele contidos para necessidades de seu desenvolvimento personalizado. Pode enviar este registo de diagnósticos para [registos do Azure Monitor](../azure-monitor/insights/azure-sql.md), [Event Hubs do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage), ou uma solução de terceiros para DevOps personalizado de alertas e relatórios capacidades.

## <a name="log-header"></a>Cabeçalho de registo

O registo de diagnóstico utiliza o formato padrão de JSON para as descobertas de informações inteligentes de saída. A propriedade de categoria exato para aceder a um registo de informações inteligentes é o valor fixo "SQLInsights".

O cabeçalho do registo é comum e consiste o carimbo de hora (TimeGenerated) que mostra que foi criada uma entrada. Ele também inclui um ID de recurso (ResourceId) que se refere à base de dados do SQL específica relacionada com a entrada. A categoria (categoria), o nível (nível) e o nome da operação (Oeprationname) são fixos propriedades cujos valores não mudam. Eles indicam que a entrada de registo se a fins informativa e que são provenientes de informações inteligentes (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID do problema e a base de dados afetados

A propriedade de identificação do problema (issueId_d) fornece uma forma de forma exclusiva rastrear problemas de desempenho até resolvido. Vários registos de eventos no registo de comunicar o estado do mesmo problema irão partilhar o mesmo ID de problema.

Juntamente com o ID de problema, o registo de diagnóstico relatórios de início (intervalStartTime_t) e de carimbos de hora de fim (intervalEndTme_t) do evento específico relacionado com um problema que é relatado no log de diagnóstico.

A propriedade de conjunto elástico (elasticPoolName_s) indica qual conjunto elástico pertence a base de dados com um problema. Se a base de dados não faz parte de um conjunto elástico, esta propriedade tem nenhum valor. A base de dados em que foi detetado um problema é divulgado na propriedade de nome (databaseName_s) da base de dados.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detetados

A secção seguinte do registo de desempenho de informações inteligentes contém problemas de desempenho que foram detetados através de inteligência artificial incorporada. Deteções forem reveladas nas propriedades no registo de diagnósticos do JSON. Estas deteções são compostos por categoria de um problema, o impacto do problema, as consultas afetadas e as métricas. As propriedades de detecções podem conter vários problemas de desempenho que foram detetados.

Problemas de desempenho detetados são comunicados com a seguinte estrutura de propriedade de deteções:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Padrões de desempenho detetável e os detalhes que são produzidos no log de diagnóstico são fornecidos na tabela seguinte.

### <a name="detection-category"></a>Categoria de deteção

A propriedade category (categoria) descreve a categoria de padrões de desempenho detetável. Veja a seguinte tabela para todas as categorias de possíveis de padrões de desempenho detetável. Para obter mais informações, consulte [resolver problemas de desempenho de base de dados com informações inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

Dependendo do problema de desempenho detetado, os detalhes de saída no diagnóstico do ficheiro de registo diferem em conformidade.

| Padrões de desempenho detetável | Detalhes de saída |
| :------------------- | ------------------- |
| Alcance limites de recursos | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentagem de consumo de recursos</li> |
| Aumento de carga de trabalho | <li>Número de consultas cuja execução aumentou</li><li>Consulta de hashes de consultas com o maior contributo para o aumento de carga de trabalho</li> |
| Pressão de memória | <li>Funcionário da área de memória</li> |
| Bloqueio | <li>Afetados hashes de consulta</li><li>Bloqueio de hashes de consulta</li> |
| MAXDOP maior | <li>Hashes de consulta</li><li>Tempos de espera CXP</li><li>Tempos de espera</li> |
| Pagelatch contenção | <li>Consultar os hashes de consultas, fazendo com que a contenção</li> |
| Índice em falta | <li>Hashes de consulta</li> |
| Nova Consulta | <li>Hash de consulta de novas consultas</li> |
| Estatística de espera invulgar | <li>Tipos de espera invulgar</li><li>Hashes de consulta</li><li>Tempos de espera de consulta</li> |
| Contenção do TempDB | <li>Consultar os hashes de consultas, fazendo com que a contenção</li><li>Atribuição de consulta para a base de dados pagelatch contenção tempo de espera geral [%]</li> |
| Conjunto elástico insuficiência de DTU | <li>Conjunto elástico</li><li>Base de dados de consumo de DTU superior</li><li>Percentagem de DTU usado pelo consumidor superior do conjunto</li> |
| Regressão do plano | <li>Hashes de consulta</li><li>Bom plano IDs</li><li>IDs de plano incorreto</li> |
| Alteração do valor de configuração do âmbito de base de dados | <li>Alterações de configuração do âmbito de base de dados em comparação comparadas os valores predefinidos</li> |
| Cliente lentas | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Mudança para versão anterior do escalão de preço | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

O impacto (impacto) propriedade descreve quanto um comportamento detetado contribuíram para o problema que está a ter uma base de dados. Intervalo de impactos de 1 a 3, com 3 como a contribuição de mais alta, 2 como moderada e 1 como a contribuição mais baixa. O valor de impacto pode ser utilizado como entrada para a automatização de alerta personalizada, consoante as suas necessidades específicas. As consultas de propriedade afetados (QueryHashes) fornecem uma lista da consulta hashes que foram afetados por uma deteção específica.

### <a name="impacted-queries"></a>Consultas afetadas

A secção seguinte do registo de informações inteligentes fornece informações sobre consultas específicas que foram afetados por problemas de desempenho detetados. Estas informações são divulgadas como uma matriz de objetos incorporados na propriedade impact_s. A propriedade impacto é composta por entidades e as métricas. Entidades fazer referência a uma consulta específica (tipo: Consulta). O hash de consulta exclusivas é divulgado sob a propriedade value (valor). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor que indica um problema de desempenho detetados.

No exemplo seguinte do registo, a consulta com o hash 0x9102EXZ4 foi detetada a ter uma maior duração de execução (métrica: DurationIncreaseSeconds). O valor de 110 segundos indica que esta consulta específica demorou 110 segundos já está a executar. Uma vez que várias consultas podem ser excluídas, esta secção de registo específico pode incluir várias entradas de consulta.

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

A unidade de medida para cada métrica reportada é fornecida sob a propriedade de métrica (Métrica) com os valores possíveis dos segundos, o número e percentagem. O valor de uma medida métrica é reportado na propriedade value (valor).

A propriedade DurationIncreaseSeconds fornece a unidade de medida em segundos. A unidade de CriticalErrorCount de medida é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de análise e melhoria da causa raiz

A última parte do registo de desempenho de informações inteligentes diz respeito a análise de causa raiz automatizadas do problema de degradação de desempenho identificados. As informações são apresentadas no verbosidade amigável a humanos na propriedade de (rootCauseAnalysis_s) de análise de causa raiz. Recomendações de melhorias estão incluídas no registo de sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Pode utilizar o registo de desempenho de informações inteligentes com [registos do Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para DevOps personalizado de alertas e dos recursos de relatórios.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [informações inteligentes](sql-database-intelligent-insights.md) conceitos.
- Saiba como [resolver problemas de desempenho de base de dados do Azure SQL com informações inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).
- Saiba como [monitorizar a base de dados do Azure SQL com a análise de SQL do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Saiba como [recolher e consumir dados de registo dos seus recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



