---
title: Monitoring Azure Functions with Azure Monitor Logs
description: Learn how to use Azure Monitor Logs with Azure Functions to monitor function executions.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: 9aac6662304395b1bce5dfc21770d296f6a4f2ab
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226854"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoring Azure Functions with Azure Monitor Logs

Azure Functions offers an integration with [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) to monitor functions. This article shows you how to configure Azure Functions to send system-generated and user-generated logs to Azure Monitor Logs.

Azure Monitor Logs gives you the ability to consolidate logs from different resources in the same workspace, where it can be analyzed with [queries](../azure-monitor/log-query/log-query-overview.md) to quickly retrieve, consolidate, and analyze collected data.  You can create and test queries using [Log Analytics](../azure-monitor/log-query/portals.md) in the Azure portal and then either directly analyze the data using these tools or save queries for use with [visualizations](../azure-monitor/visualizations.md) or [alert rules](../azure-monitor/platform/alerts-overview.md).

Azure Monitor uses a version of the [Kusto query language](/azure/kusto/query/) used by Azure Data Explorer that is suitable for simple log queries but also includes advanced functionality such as aggregations, joins, and smart analytics. You can quickly learn the query language using [multiple lessons](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integration with Azure Monitor Logs is currently in public preview for function apps running on Windows Consumption, Premium, and Dedicated hosting plans.

## <a name="setting-up"></a>Setting up

From the Monitoring section, select **Diagnostic settings** and then click **Add**.

![Add a diagnostic setting](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

In the setting page, choose **Send to Log Analytics**, and under **LOG** choose **FunctionAppLogs**, this table contains the desired logs.

![Add a diagnostic setting](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>User generated logs

To generate custom logs, you can use the specific logging statement depending on your language, here are sample code snippets:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Querying the logs

To query the generated logs, go to the log analytics workspace and click **Logs**.

![Query window in LA workspace](media/functions-monitor-log-analytics/querying.png)

Azure Functions writes all logs to **FunctionAppLogs** table, here are some sample queries.

### <a name="all-logs"></a>All logs

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>A specific function logs

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Exceções

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Passos seguintes

- Review the [Azure Functions overview](functions-overview.md)
- Learn more about [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)
- Learn more about the [query language](../azure-monitor/log-query/get-started-queries.md).
