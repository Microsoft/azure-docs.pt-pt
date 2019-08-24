---
title: Monitorar carga de trabalho-portal do Azure | Microsoft Docs
description: Monitorar SQL Data Warehouse do Azure usando o portal do Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 176762e0d1c007f924d779a1b77dd52c7ed56e01
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981493"
---
# <a name="monitor-workload---azure-portal"></a>Monitorar carga de trabalho-portal do Azure

Este artigo descreve como usar o portal do Azure para monitorar sua carga de trabalho. Isso inclui a configuração de logs de Azure Monitor para investigar a execução da consulta e as tendências de carga de trabalho usando o log Analytics para o [SQL data warehouse do Azure](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- SQL Data Warehouse do Azure: Coletaremos logs para um SQL Data Warehouse. Se você não tiver um SQL Data Warehouse provisionado, consulte as instruções em [criar um SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho Log Analytics

Navegue até a folha procurar por Log Analytics espaços de trabalho e crie um espaço de trabalho 

![Áreas de trabalho do Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Adicionar espaço de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Adicionar espaço de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Para obter mais detalhes sobre espaços de trabalho, visite a [documentação](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)a seguir.

## <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico 

Defina as configurações de diagnóstico para emitir logs de seu SQL Data Warehouse. Os logs consistem em exibições de telemetria do seu data warehouse equivalente às DMVs de solução de problemas de desempenho mais usadas para SQL Data Warehouse. Atualmente, há suporte para as seguintes exibições:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Habilitando logs de diagnóstico](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Os logs podem ser emitidos para o armazenamento do Azure, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar logs](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas no Log Analytics

Navegue até o espaço de trabalho Log Analytics onde você pode fazer o seguinte:

- Analisar logs usando consultas de log e salvar consultas para reutilização
- Salvar consultas para reutilização
- Criar alertas de log
- Fixar resultados da consulta em um painel

Para obter detalhes sobre os recursos das consultas de log, visite a [documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)a seguir.

![Editor de espaço de trabalho Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics consultas de espaço de trabalho](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Consultas de log de exemplo



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

Agora que você configurou e configurou os logs do Azure monitor, [Personalize os painéis do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) para compartilhar em sua equipe.
