---
title: Carga de trabalho do monitor - portal do Azure | Documentos da Microsoft
description: Monitor do Azure SQL Data Warehouse no portal do Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405786"
---
# <a name="monitor-workload---azure-portal"></a>Carga de trabalho do monitor - portal do Azure

Este artigo descreve como utilizar o portal do Azure para monitorizar a carga de trabalho. Isto inclui configurar registos de Monitor do Azure para investigar as tendências de execução e a carga de trabalho de consulta com o log analytics para [do Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Armazém de dados SQL do Azure: Podemos irá ser a recolher dados para um SQL data warehouse. Se não tiver aprovisionado um SQL data warehouse, consulte as instruções em [criar um SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

Navegue para o painel de navegação para áreas de trabalho do Log Analytics e crie uma área de trabalho 

![Áreas de trabalho do Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Adicionar área de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Adicionar área de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Para obter mais detalhes sobre as áreas de trabalho, visite o seguinte procedimento [documentação](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico 

Configure definições de diagnóstico para emitir os registos do seu SQL data warehouse. Os registos são compostos por vistas de telemetria do seu armazém de dados equivalente para o desempenho mais comumente usado DMVs de resolução de problemas para o SQL Data Warehouse. Atualmente são suportadas as seguintes vistas:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Ativar registos de diagnóstico](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Registos podem ser emitidos para o armazenamento do Azure, o Stream Analytics ou o Log Analytics. Neste tutorial, selecione o Log Analytics.

![Especificar os registos](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas do Log Analytics

Navegue até à sua área de trabalho do Log Analytics onde pode fazer o seguinte:

- Analisar os registos através de consultas de registo e guardar consultas para reutilização
- Guardar consultas para reutilização
- Criar alertas do registo
- Resultados da consulta de PIN a um dashboard

Para obter detalhes sobre as capacidades de consultas de registo, visite o seguinte procedimento [documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor de área de trabalho do log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Consultas de área de trabalho de análise de registo](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Consultas de registo de exemplo



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
## <a name="next-steps"></a>Passos Seguintes

Agora que tiver definido e configurado os registos do Azure monitor, [personalize dashboards do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) para partilhar com a sua equipa.
