---
title: Monitor de carga de trabalho - Portal Azure
description: Monitor Synapse SQL utilizando o portal Azure
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568272"
---
# <a name="monitor-workload---azure-portal"></a>Monitor de carga de trabalho - Portal Azure

Este artigo descreve como utilizar o portal Azure para monitorizar a sua carga de trabalho. Isto inclui a criação de Registos do Monitor Azure para investigar as tendências de execução de consultas e carga de trabalho utilizando a análise de registos para [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Piscina SQL: Vamos recolher registos para uma piscina SQL. Se não tiver uma piscina SQL fornecida, consulte as instruções na [Criação de uma piscina SQL](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

Navegue até à lâmina de navegação para espaços de trabalho log analytics e crie um espaço de trabalho

![Áreas de trabalho do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![A screenshot mostra os espaços de trabalho do Log Analytics onde pode selecionar Add.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![A screenshot mostra o espaço de trabalho Log Analytics onde pode introduzir valores.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Para mais detalhes sobre espaços de trabalho, visite a seguinte [documentação.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Ligue registos de recursos

Configure as definições de diagnóstico para emitir registos da sua piscina SQL. Os registos consistem em vistas de telemetria equivalentes às DMVs de resolução de problemas de desempenho mais utilizadas. Atualmente são apoiados os seguintes pontos de vista:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Ativar registos de recursos](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Os registos podem ser emitidos para Azure Storage, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar registos](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas contra o Log Analytics

Navegue para o seu espaço de trabalho Log Analytics onde pode fazer o seguinte:

- Analise os registos usando consultas de registo e guarde consultas para reutilização
- Guardar consultas para reutilização
- Criar alertas de registos
- Pin consulta resultados para um dashboard

Para mais informações sobre as capacidades das consultas de registo, visite a seguinte [documentação.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Log Analytics editor de espaço de trabalho](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Consultas de espaço de trabalho Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Agora que configuraram e configuraram registos de monitores Azure, [personalize os dashboards Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para partilhar em toda a sua equipa.