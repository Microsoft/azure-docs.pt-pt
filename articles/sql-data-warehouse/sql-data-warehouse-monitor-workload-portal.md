---
title: Monitorizar a carga de trabalho - Portal Azure
description: Monitor SQL Analytics utilizando o portal Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7e93aee405d8a66d850a4e3f07f2e788f1004ef8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197246"
---
# <a name="monitor-workload---azure-portal"></a>Monitorizar a carga de trabalho - Portal Azure

Este artigo descreve como usar o portal Azure para monitorizar a sua carga de trabalho. Isto inclui a criação de registos do Monitor Azure para investigar as tendências de execução de consultas e de carga de trabalho utilizando análises de registo para [o SQL Analytics](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Piscina SQL: Vamos recolher registos para uma piscina SQL. Se não tiver uma piscina SQL aprovisionada, consulte as instruções em [Create a SQL pool](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho log Analytics

Navegue para a lâmina de navegação para espaços de trabalho log Analytics e crie um espaço de trabalho 

![Áreas de trabalho do Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Adicionar espaço de trabalho analytics](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Adicionar espaço de trabalho analytics](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Para mais detalhes sobre espaços de trabalho, visite a seguinte [documentação.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)

## <a name="turn-on-diagnostic-logs"></a>Ligue os registos de diagnóstico

Configure as definições de diagnóstico para emitir registos da sua piscina SQL. Os registos consistem em vistas de telemetria equivalentes às DMVs de resolução de problemas de desempenho mais comumente usadas. Atualmente, são apoiadas as seguintes opiniões:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Habilitar registos de diagnóstico](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Os registos podem ser emitidos para o Armazenamento Azure, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar registos](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas contra log analytics

Navegue para o seu espaço de trabalho Log Analytics onde pode fazer o seguinte:

- Analise os registos usando consultas de registo e guarde consultas para reutilização
- Guarde as consultas para reutilizar
- Criar alertas de registos
- Resultados da consulta de pin para um dashboard

Para mais informações sobre as capacidades das consultas de registo, visite a seguinte [documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor de espaço de trabalho Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Consultas de espaço de trabalho de Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

Agora que criou e configurou os registos de monitores Azure, [personalize os dashboards Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) para partilhar em toda a sua equipa.
