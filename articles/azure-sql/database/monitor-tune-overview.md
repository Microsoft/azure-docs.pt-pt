---
title: Ajuste de monitorização e desempenho
description: Uma visão geral das capacidades de monitorização e afinação de desempenho e metodologia na Base de Dados Azure SQL e na Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 7ac3a2947c84eca0e22e0888495f9992222d4dd6
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220535"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorização e afinação de desempenho na Base de Dados Azure SQL e na Azure SQL Gestão de Instâncias
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Para monitorizar o desempenho de uma base de dados na Base de Dados Azure SQL e na Azure SQL Managed Instance, comece por monitorizar os recursos de CPU e IO utilizados pela sua carga de trabalho em relação ao nível de desempenho da base de dados que escolheu na seleção de um determinado nível de serviço e nível de desempenho. Para isso, a Azure SQL Database e a Azure SQL Managed Instance emitem métricas de recursos que podem ser vistas no portal Azure ou utilizando uma destas ferramentas de gestão do SQL Server: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

A Azure SQL Database fornece uma série de Consultores de Bases de Dados para fornecer recomendações inteligentes de afinação de desempenho e opções de afinação automática para melhorar o desempenho. Além disso, o Query Performance Insight mostra-lhe detalhes sobre as consultas responsáveis pela utilização mais CPU e IO para bases de dados individuais e aglineadas.

A Azure SQL Database e Azure SQL Managed Instance fornecem capacidades avançadas de monitorização e afinação apoiadas pela inteligência artificial para o ajudar na resolução de problemas e na maximização do desempenho das suas bases de dados e soluções. Pode optar por configurar a [exportação de streaming](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) destes [Insights Inteligentes](intelligent-insights-overview.md) e outros registos e métricas de recursos de base de dados para um dos vários destinos para consumo e análise, nomeadamente utilizando [a SQL Analytics).](../../azure-monitor/insights/azure-sql.md) O Azure SQL Analytics é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados em escala e em várias subscrições numa única vista. Para obter uma lista dos registos e métricas que pode exportar, consulte [a telemetria de diagnóstico para exportação](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

Finalmente, o SQL Server tem as suas próprias capacidades de monitorização e diagnóstico que a SQL Database e a SQL Managed Instance alavancam, tais como [loja de consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [vistas dinâmicas de gestão (DMVs)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consulte [a monitorização utilizando DMVs](monitoring-with-dmvs.md) para scripts para monitorizar uma variedade de problemas de desempenho.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Capacidades de monitorização e afinação no portal Azure

No portal Azure, a Azure SQL Database e a Azure SQL Managed Instance fornecem monitorização das métricas de recursos. Além disso, a Azure SQL Database fornece consultores de base de dados e o Query Performance Insight fornece recomendações de sintonização de consulta e análise de desempenho de consulta. Finalmente, no portal Azure, pode ativar automaticamente para [servidores SQL lógicos](logical-servers.md) e suas bases de dados únicas e agitadas.

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database e Azure SQL Managed Instance monitorização de recursos

Pode monitorizar rapidamente uma variedade de métricas de recursos no portal Azure na vista **Métricas.** Estas métricas permitem-lhe ver se uma base de dados está a atingir 100% dos recursos do processador, da memória ou da IO. Alta percentagem de DTU ou processador, bem como uma elevada percentagem de IO, indica que a sua carga de trabalho pode necessitar de mais recursos de CPU ou IO. Também pode indicar consultas que precisam de ser otimizadas.

  ![Métricas de recursos](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Assessores de base de dados na Base de Dados Azure SQL

A Azure SQL Database inclui [consultores de base de dados](database-advisor-implement-performance-recommendations.md) que fornecem recomendações de afinação de desempenho para bases de dados individuais e agrâmias. Estas recomendações estão disponíveis no portal Azure, bem como através da utilização [do PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Também pode [ativar a sintonização automática para](automatic-tuning-overview.md) que o Azure SQL Database possa implementar automaticamente estas recomendações de afinação.

### <a name="query-performance-insight-in-azure-sql-database"></a>Insight de desempenho de consulta na base de dados Azure SQL

[O Insight de Desempenho](query-performance-insight-use.md) de Consulta mostra o desempenho no portal Azure de consultas de topo consumistas e mais longas para bases de dados individuais e ageyadas.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Gerar avaliações inteligentes de problemas de desempenho

[Insights Inteligentes](intelligent-insights-overview.md) para Azure SQL Database e Azure SQL Managed Instance usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho. O Intelligent Insights deteta automaticamente problemas de desempenho com bases de dados baseadas em tempos de espera de execução de consultas, erros ou intervalos de tempo. Uma vez detetado, é realizada uma análise detalhada que gera um registo de recursos (chamado SQLInsights) com uma [avaliação inteligente dos problemas](intelligent-insights-troubleshoot-performance.md). Esta avaliação consiste numa análise de causa-raiz da questão do desempenho da base de dados e, sempre que possível, de recomendações para melhorias de desempenho.

Intelligent Insights é uma capacidade única da inteligência incorporada Azure que fornece o seguinte valor:

- Monitorização proativa
- Insights de desempenho personalizados
- Deteção precoce da degradação do desempenho da base de dados
- Análise de causa raiz de problemas detetados
- Recomendações de melhoria de desempenho
- Reduzir a capacidade em centenas de milhares de bases de dados
- Impacto positivo para os recursos da DevOps e o custo total de propriedade

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Permitir a exportação de métricas e registos de recursos

Pode ativar e configurar a [exportação de telemetria de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para um dos vários destinos, incluindo o registo de recursos Intelligent Insights. Utilize [a SQL Analytics](../../azure-monitor/insights/azure-sql.md) e outras capacidades para consumir esta telemetria de diagnóstico adicional para identificar e resolver problemas de desempenho.

Configura definições de diagnóstico para transmitir categorias de métricas e registos de recursos para bases de dados individuais, bases de dados agimidas, piscinas elásticas, instâncias geridas e bases de dados de instâncias para um dos seguintes recursos Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics espaço de trabalho no Azure Monitor

Pode transmitir métricas e registos de recursos para um [espaço de trabalho Log Analytics no Azure Monitor](../../azure-monitor/platform/resource-logs-collect-workspace.md). Os dados transmitidos aqui podem ser consumidos pela [SQL Analytics,](../../azure-monitor/insights/azure-sql.md)que é uma solução de monitorização apenas na nuvem que fornece uma monitorização inteligente das suas bases de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitorização recolhidos e também permitem-lhe alavancar outras funcionalidades do Azure Monitor, tais como alertas e visualizações.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Pode transmitir métricas e registos de recursos para [Azure Event Hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md). Streaming de telemetria de diagnóstico para centros de eventos para fornecer a seguinte funcionalidade:

- **Registos de fluxo para sistemas de registo e telemetria de terceiros**

  Transmita todas as suas métricas e registos de recursos para um único centro de eventos para registar dados de registo de tubos para uma ferramenta siem de terceiros ou ferramenta de análise de registo.
- **Construir uma plataforma de telemetria e de registo personalizado**

  A natureza altamente escalável de publicação-subscrição dos centros de eventos permite-lhe ingerir métricas e registos de recursos de forma flexível numa plataforma de telemetria personalizada. Consulte [Designing e Dimensionamento de uma Plataforma global de Telemetria à Escala Global nos Hubs de Eventos Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para mais detalhes.
- **Ver saúde do serviço através do streaming de dados para Power BI**

  Utilize centros de eventos, Stream Analytics e Power BI para transformar os seus dados de diagnóstico em insights quase em tempo real nos seus serviços Azure. Consulte [Stream Analytics e Power BI: Um dashboard de análise em tempo real para transmitir dados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) para obter detalhes sobre esta solução.

### <a name="azure-storage"></a>Storage do Azure

Transmita métricas e registos de recursos para [o Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md). Utilize o armazenamento Azure para arquivar grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

## <a name="use-extended-events"></a>Use eventos prolongados 

Além disso, pode utilizar [eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) no SQL Server para monitorização e resolução de problemas avançados. A arquitetura de eventos alargados permite que os utilizadores recolham tanto ou tão poucos dados quanto necessários para resolver problemas ou identificar um problema de desempenho. Para obter informações sobre a utilização de eventos alargados na Base de Dados Azure SQL, consulte [eventos estendidos na Base de Dados Azure SQL](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre recomendações inteligentes de desempenho para bases de dados individuais e agramadas, consulte [recomendações de desempenho do conselheiro de base de dados](database-advisor-implement-performance-recommendations.md).
- Para obter mais informações sobre a monitorização automática do desempenho da base de dados com diagnósticos automatizados e análise de causa de raiz de problemas de desempenho, consulte [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
