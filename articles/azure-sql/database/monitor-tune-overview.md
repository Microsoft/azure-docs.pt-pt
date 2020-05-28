---
title: Ajuste de monitorização e desempenho
description: Uma visão geral das capacidades de monitorização e afinação de desempenho e metodologia na Base de Dados Azure SQL e na Instância Gerida Azure SQL.
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
ms.openlocfilehash: ab738e829b9c3e5a5c71ea8baeff67772723b64e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048869"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorização e sintonização de desempenho na Base de Dados Azure SQL e na Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Para monitorizar o desempenho de uma base de dados em Azure SQL Database e Azure SQL Managed Instance, comece por monitorizar os recursos cpu e IO utilizados pela sua carga de trabalho em relação ao nível de desempenho da base de dados que escolheu na seleção de um determinado nível de serviço e nível de desempenho. Para isso, a Azure SQL Database e a Azure SQL Managed Instance emitem métricas de recursos que podem ser vistas no portal Azure ou utilizando uma destas ferramentas de gestão SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

A Base de Dados Azure SQL fornece uma série de Consultores de Base de Dados para fornecer recomendações inteligentes de afinação de desempenho e opções de afinação automática para melhorar o desempenho. Além disso, a Query Performance Insight mostra-lhe detalhes sobre as consultas responsáveis pela utilização mais cpu e IO para bases de dados individuais e reunidas.

A Azure SQL Database e a Azure SQL Managed Instance fornecem capacidades avançadas de monitorização e afinação apoiadas pela inteligência artificial para o ajudar na resolução de problemas e na maximização do desempenho das suas bases de dados e soluções. Pode escolher configurar a [exportação](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) de streaming destes [Smart Insights](intelligent-insights-overview.md) e outros registos e métricas de recursos de base de dados para um dos vários destinos para consumo e análise, nomeadamente utilizando o [SQL Analytics).](../../azure-monitor/insights/azure-sql.md) O Azure SQL Analytics é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados em escala e em várias subscrições numa única vista. Para obter uma lista dos registos e métricas que pode exportar, consulte [a telemetria de diagnóstico para exportação](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

Finalmente, a SQL tem as suas próprias capacidades de monitorização e diagnóstico com a [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [pontos de vista dinâmicos de gestão (DMVs)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consulte [a monitorização utilizando DMVs](monitoring-with-dmvs.md) para scripts para monitorizar uma variedade de problemas de desempenho.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Capacidades de monitorização e afinação no portal Azure

No portal Azure, a Azure SQL Database e a Azure SQL Managed Instance fornecem monitorização das métricas de recursos. Além disso, a Base de Dados Azure SQL fornece consultores de base de dados e a Consulta Performance Insight fornece recomendações de afinação de consultas e análise de desempenho de consulta. Finalmente, no portal Azure, pode ativar automaticamente para [servidores SQL lógicos](logical-servers.md) e suas bases de dados únicas e reunidas.

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Base de Dados Azure SQL e monitorização de recursos geridos azure SQL

Pode monitorizar rapidamente uma variedade de métricas de recursos no portal Azure na vista **Métricas.** Estas métricas permitem-lhe ver se uma base de dados está a atingir 100% dos recursos do processador, memória ou IO. Uma elevada percentagem de DTU ou processador, bem como uma elevada percentagem de IO, indica que a sua carga de trabalho pode necessitar de mais recursos de CPU ou IO. Também pode indicar consultas que precisam de ser otimizadas.

  ![Métricas de recursos](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Consultores de base de dados na Base de Dados Azure SQL

A Base de Dados Azure SQL inclui [consultores](database-advisor-implement-performance-recommendations.md) de base de dados que fornecem recomendações de afinação de desempenho para bases de dados individuais e reunidas. Estas recomendações estão disponíveis no portal Azure, bem como através da utilização do [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) Também pode ativar a [sintonização automática](automatic-tuning-overview.md) para que a Base de Dados Azure SQL possa implementar automaticamente estas recomendações de afinação.

### <a name="query-performance-insight-in-azure-sql-database"></a>Consulta Performance Insight na Base de Dados Azure SQL

[Consulta Performance Insight](query-performance-insight-use.md) mostra o desempenho no portal Azure de consultas de topo consumistas e mais longas para bases de dados individuais e reunidas.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Gerar avaliações inteligentes de problemas de desempenho

[Smart Insights](intelligent-insights-overview.md) para Azure SQL Database e Azure SQL Managed Instance usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho. A Intelligent Insights deteta automaticamente problemas de desempenho com bases de dados baseadas em tempos de espera de execução de consultas, erros ou intervalos. Uma vez detetada, é realizada uma análise detalhada que gera um registo de recursos (chamado SQLInsights) com uma [avaliação inteligente dos problemas](intelligent-insights-troubleshoot-performance.md). Esta avaliação consiste numa análise de causas fundamentais do problema de desempenho da base de dados e, sempre que possível, recomendações para melhorias de desempenho.

Smart Insights é uma capacidade única da inteligência incorporada do Azure que fornece o seguinte valor:

- Monitorização proativa
- Insights de desempenho personalizados
- Deteção precoce da degradação do desempenho da base de dados
- Análise de causa seleções de problemas detetados
- Recomendações de melhoria do desempenho
- Aumentar a capacidade em centenas de milhares de bases de dados
- Impacto positivo nos recursos da DevOps e o custo total da propriedade

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Permitir a exportação de métricas e registos de recursos em streaming

Pode ativar e configurar a [exportação de telemetria de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para um dos vários destinos, incluindo o registo de recursos Intelligent Insights. Utilize o [SQL Analytics](../../azure-monitor/insights/azure-sql.md) e outras capacidades para consumir esta telemetria de diagnóstico adicional para identificar e resolver problemas de desempenho.

Configura as definições de diagnóstico para transmitir categorias de métricas e registos de recursos para bases de dados únicas, bases de dados agrupadas, piscinas elásticas, instâncias geridas e bases de dados de instâncias para um dos seguintes recursos Do Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics espaço de trabalho no monitor Azure

Pode transmitir métricas e registos de recursos para um espaço de [trabalho de Log Analytics no Monitor Azure](../../azure-monitor/platform/resource-logs-collect-workspace.md). Os dados transmitidos aqui podem ser consumidos pela [SQL Analytics,](../../azure-monitor/insights/azure-sql.md)que é uma solução de monitorização em nuvem que fornece uma monitorização inteligente das suas bases de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitorização recolhidos e também permite alavancar outras funcionalidades do Monitor do Azure, como alertas e visualizações.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Pode transmitir métricas e registos de recursos para Hubs de [Eventos Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md). Streaming de telemetria de diagnóstico para centros de eventos para fornecer a seguinte funcionalidade:

- **Registos de fluxo para sistemas de registo e telemetria de terceiros**

  Transmita todas as suas métricas e registos de recursos para um único centro de eventos para canalizar dados de registo para uma ferramenta de análise de registo de terceiros.
- **Construa uma plataforma personalizada de telemetria e exploração madeireira**

  A natureza altamente escalável de centros de eventos permite-lhe ingerir flexionamente métricas e registos de recursos numa plataforma de telemetria personalizada. Consulte [design e dimensionamento de uma plataforma de telemetria](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) de escala global em Hubs de eventos Azure para mais detalhes.
- **Ver saúde do serviço através do streaming de dados para Power BI**

  Utilize Hubs de Eventos, Stream Analytics e Power BI para transformar os seus dados de diagnóstico em informações quase em tempo real sobre os seus serviços Azure. Consulte [stream analytics e Power BI: Um dashboard de análise em tempo real para transmitir dados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) para obter detalhes sobre esta solução.

### <a name="azure-storage"></a>Storage do Azure

Fluxo métricas e registos de recursos para [armazenamento Azure](../../azure-monitor/platform/resource-logs-collect-storage.md). Utilize o armazenamento Azure para arquivar grandes quantidades de telemetria de diagnóstico para uma fração do custo das duas opções de streaming anteriores.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Utilize eventos alargados no motor de base de dados SQL

Além disso, pode utilizar [eventos alargados](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) em SQL para monitorização avançada adicional e resolução de problemas. A arquitetura de eventos alargados permite que os utilizadores recolham tanto ou tão poucos dados quanto necessário para resolver problemas ou identificar um problema de desempenho. Para obter informações sobre a utilização de eventos alargados na Base de Dados Azure SQL, consulte [eventos estendidos na Base de Dados Azure SQL](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre recomendações inteligentes de desempenho para bases de dados individuais e reunidas, consulte recomendações de desempenho do consultor de base de [dados.](database-advisor-implement-performance-recommendations.md)
- Para obter mais informações sobre a monitorização automática do desempenho da base de dados com diagnósticos automatizados e análise de causas fundamentais de problemas de desempenho, consulte [O Azure SQL Intelligent Insights](intelligent-insights-overview.md).
