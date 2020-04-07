---
title: Monitorizar a carga de trabalho - Portal Azure
description: Monitor Synapse SQL utilizando o portal Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743212"
---
# <a name="monitor-workload---azure-portal"></a>Monitorizar a carga de trabalho - Portal Azure

Este artigo descreve como usar o portal Azure para monitorizar a sua carga de trabalho. Isto inclui a criação de registos do Monitor Azure para investigar as tendências de execução de consultas e de carga de trabalho utilizando análises de registo para [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Piscina SQL: Vamos recolher registos para uma piscina SQL. Se não tiver uma piscina SQL aprovisionada, consulte as instruções em [Create a SQL pool](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

Navegue para a lâmina de navegação para espaços de trabalho log Analytics e crie um espaço de trabalho

![Áreas de trabalho do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Adicionar espaço de trabalho analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Adicionar espaço de trabalho analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Para mais detalhes sobre espaços de trabalho, visite a seguinte [documentação.](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-diagnostic-logs"></a>Ligue os registos de diagnóstico

Configure as definições de diagnóstico para emitir registos da sua piscina SQL. Os registos consistem em vistas de telemetria equivalentes às DMVs de resolução de problemas de desempenho mais comumente usadas. Atualmente, são apoiadas as seguintes opiniões:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Habilitar registos de diagnóstico](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Os registos podem ser emitidos para o Armazenamento Azure, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar registos](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas contra log analytics

Navegue para o seu espaço de trabalho Log Analytics onde pode fazer o seguinte:

- Analise os registos usando consultas de registo e guarde consultas para reutilização
- Guarde as consultas para reutilizar
- Criar alertas de registos
- Resultados da consulta de pin para um dashboard

Para mais informações sobre as capacidades das consultas de registo, visite a seguinte [documentação](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Editor de espaço de trabalho Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Consultas de espaço de trabalho de Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Consultas de registo de amostras

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e configurou os registos de monitores Azure, [personalize os dashboards Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para partilhar em toda a sua equipa.
