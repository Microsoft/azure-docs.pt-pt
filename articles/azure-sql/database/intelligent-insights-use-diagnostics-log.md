---
title: Registo de diagnósticos de desempenho de Insights Inteligentes
description: Intelligent Insights fornece um registo de diagnóstico de Azure SQL Database e Azure SQL Gerenciados Problemas de desempenho
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 6720058ce6e7614284111a75a2ab3a91525df1a3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488770"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Utilize o registo de diagnósticos de desempenho de Insights Inteligentes da Base de Dados Azure SQL e dos problemas de desempenho da Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Esta página fornece informações sobre como utilizar o registo de diagnósticos de desempenho gerado por [Insights Inteligentes](intelligent-insights-overview.md) da Base de Dados SQL Azure e problemas de desempenho de exemplo geridos Azure SQL, o seu formato e os dados que contém para as suas necessidades de desenvolvimento personalizado. Pode enviar este registo de diagnóstico para [registos do Azure Monitor](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)ou uma solução de terceiros para capacidades de alerta e reporte de DevOps personalizados.

> [!NOTE]
> Insights inteligentes é uma característica de pré-visualização, não disponível nas seguintes regiões: Europa Ocidental, Norte da Europa, EUA 1 e Leste dos EUA 1.

## <a name="log-header"></a>Cabeçalho de registo

O registo de diagnósticos utiliza o formato padrão JSON para obter conclusões de Insights Inteligentes. A propriedade de categoria exata para aceder a um registo de Insights Inteligentes é o valor fixo "SQLInsights".

O cabeçalho do log é comum e consiste na marca de tempo (TimeGenerated) que mostra quando uma entrada foi criada. Também inclui um ID de recurso (ResourceId) que se refere à base de dados específica a que a entrada se refere. A categoria (Categoria), nível (Nível) e nome de operação (Operação Natal) são propriedades fixas cujos valores não mudam. Indicam que a entrada de registo é informativa e que provém de Insights Inteligentes (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Emitir ID e base de dados afetada

A propriedade de identificação de problemas de emissão (issueId_d) fornece uma forma de rastrear exclusivamente problemas de desempenho até ser resolvido. Vários registos de eventos no estado de reporte de registo da mesma emissão partilharão o mesmo ID de emissão.

Juntamente com o ID de emissão, o registo de diagnósticos relata os selos de tempo de início (intervalStartTime_t) e final (intervalEndTme_t) do evento em particular relacionado com um problema que é relatado no registo de diagnósticos.

A propriedade de piscina elástica (elasticPoolName_s) indica a que piscina elástica pertence à base de dados com um problema. Se a base de dados não faz parte de uma piscina elástica, esta propriedade não tem valor. A base de dados na qual foi detetado um problema é divulgada na propriedade do nome da base de dados (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Problemas detetados

A secção seguinte do registo de desempenho do Intelligent Insights contém problemas de desempenho que foram detetados através da inteligência artificial incorporada. As deteções são divulgadas em propriedades dentro do registo de diagnóstico json. Estas deteções consistem na categoria de um problema, no impacto da questão, nas consultas afetadas e nas métricas. As propriedades de deteção podem conter múltiplos problemas de desempenho que foram detetados.

Os problemas de desempenho detetados são reportados com a seguinte estrutura de propriedade de deteção:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Os padrões de desempenho detetáveis e os detalhes que são desatados para o registo de diagnósticos são fornecidos na tabela seguinte.

### <a name="detection-category"></a>Categoria de deteção

A categoria (categoria) de propriedade descreve a categoria de padrões de desempenho detetáveis. Consulte a tabela seguinte para todas as categorias possíveis de padrões de desempenho detetáveis. Para obter mais informações, consulte [problemas de desempenho da base de dados de resolução de problemas com Insights Inteligentes.](intelligent-insights-troubleshoot-performance.md)

Dependendo do problema de desempenho detetado, os detalhes produzidos no ficheiro de registo de diagnósticos diferem em conformidade.

| Padrões de desempenho detetáveis | Detalhes desatados |
| :------------------- | ------------------- |
| Atingir limites de recursos | <li>Recursos afetados</li><li>Hashes de consulta</li><li>Percentagem de consumo de recursos</li> |
| Aumento da carga de trabalho | <li>Número de consultas cuja execução aumentou</li><li>Consulta de consultas com maior contribuição para o aumento da carga de trabalho</li> |
| Pressão da Memória | <li>Escriturário de memória</li> |
| Bloquear | <li>Adísseras de consulta afetadas</li><li>Bloqueio de consultas hashes</li> |
| AUMENTO DO MAXDOP | <li>Hashes de consulta</li><li>Tempos de espera do CXP</li><li>Tempos de espera</li> |
| Contenção pagelatch | <li>Consultas de consultas que causam contenção</li> |
| Índice em Falta | <li>Hashes de consulta</li> |
| New Query | <li>Haxixe de consulta das novas consultas</li> |
| Estatística de espera incomum | <li>Tipos de espera incomuns</li><li>Hashes de consulta</li><li>Tempos de espera de consultas</li> |
| Contenção temporária | <li>Consultas de consultas que causam contenção</li><li>Atribuição de consulta ao tempo de espera de contenção de contenção de pagelatch de base de dados global [%]</li> |
| Escassez de DTU de piscina elástica | <li>Conjunto elástico</li><li>Base de dados de consumo de DTU</li><li>Por cento do DTU de piscina usado pelo consumidor de topo</li> |
| Regressão do Plano | <li>Hashes de consulta</li><li>Boas IDs de plano</li><li>IDs de plano mau</li> |
| alteração de valor de configuração Database-Scoped | <li>Alterações de configuração com âmbito de dados em comparação com os valores predefinidos</li> |
| Cliente lento | <li>Hashes de consulta</li><li>Tempos de espera</li> |
| Downgrade de nível de preços | <li>Notificação de texto</li> |

### <a name="impact"></a>Impacto

A propriedade de impacto (impacto) descreve o quanto um comportamento detetado contribuiu para o problema que uma base de dados está a ter. Os impactos variam de 1 a 3, com 3 como a maior contribuição, 2 como moderado, e 1 como a contribuição mais baixa. O valor de impacto pode ser usado como uma entrada para a automatização de alerta personalizado, dependendo das suas necessidades específicas. As consultas de propriedade impactadas (QueryHashes) fornecem uma lista dos hashes de consulta que foram afetados por uma deteção particular.

### <a name="impacted-queries"></a>Consultas com impacto

A secção seguinte do registo de Insights Inteligentes fornece informações sobre consultas específicas que foram afetadas pelos problemas de desempenho detetados. Esta informação é divulgada como uma variedade de objetos incorporados na propriedade impact_s. O imóvel de impacto é constituído por entidades e métricas. As entidades referem-se a uma consulta específica (Tipo: Consulta). O haxixe de consulta único é divulgado sob a propriedade de valor (Valor). Além disso, cada uma das consultas divulgadas é seguida por uma métrica e um valor, que indicam um problema de desempenho detetado.

No seguinte exemplo de registo, a consulta com o hash 0x9102EXZ4 foi detetada para ter uma duração aumentada da execução (Métrica: Duração AumentoSegundos). O valor de 110 segundos indica que esta consulta em particular demorou mais 110 segundos a ser executada. Como podem ser detetadas múltiplas consultas, esta secção de registo em particular pode incluir múltiplas entradas de consulta.

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

A unidade de medição de cada métrica reportada é fornecida sob a propriedade métrica (métrica) com os valores possíveis de segundos, número e percentagem. O valor de uma métrica medida é reportado na propriedade de valor (valor).

A propriedade DurationIncreaseconds fornece a unidade de medição em segundos. A unidade de medição CriticalErrorCount é um número que representa uma contagem de erros.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendações de análise e melhoria de causas de raiz

A última parte do registo de desempenho dos Insights Inteligentes diz respeito à análise automatizada da causa da raiz do problema de degradação do desempenho identificado. A informação aparece na verbiagem amiga do homem na propriedade de análise de causa raiz (rootCauseAnalysis_s). As recomendações de melhoria estão incluídas no registo sempre que possível.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Pode utilizar o registo de desempenho do Intelligent Insights com [registos do Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ou uma solução de terceiros para capacidades de alerta e reporte personalizadas de DevOps.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre conceitos inteligentes de [insights.](intelligent-insights-overview.md)
- Saiba como [resolver problemas de desempenho com Insights Inteligentes.](intelligent-insights-troubleshoot-performance.md)
- Saiba como monitorizar os [problemas de desempenho utilizando o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Saiba como [recolher e consumir dados de registo a partir dos seus recursos Azure.](../../azure-monitor/platform/platform-logs-overview.md)