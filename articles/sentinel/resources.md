---
title: Useful resources when working with Azure Sentinel| Microsoft Docs
description: This document provides you with a list of useful resources when working with Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 04c4192293cbfa96eefcc1c84083dd54042ebe8f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484077"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Useful resources for working with Azure Sentinel



This article lists resources that can help you get more information about working with Azure Sentinel.

Azure Logic Apps connectors: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditing and reporting
Audit logs of Azure Sentinel are maintained in [Azure Activity Logs](../azure-monitor/platform/activity-logs-overview.md).

The following supported operations can be audited.

|Operation name|    Tipo de recurso|
|----|----|
|Create or update workbook  |Microsoft.Insights/workbooks|
|Delete Workbook    |Microsoft.Insights/workbooks|
|Set Workflow   |Microsoft.Logic/workflows|
|Delete Workflow    |Microsoft.Logic/workflows|
|Create Saved Search    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Delete Saved Search    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Set Dashboard  |Microsoft.Portal/dashboards|
|Delete Dashboard   |Microsoft.Portal/dashboards|
|Update Alert Rules |Microsoft.SecurityInsights/alertRules|
|Delete Alert Rules |Microsoft.SecurityInsights/alertRules|
|Update Alert Rule Response Actions |Microsoft.SecurityInsights/alertRules|
|Delete Alert Rule Response Actions |Microsoft.SecurityInsights/alertRules|
|Update Bookmarks   |Microsoft.SecurityInsights/bookmarks|
|Delete Bookmarks   |Microsoft.SecurityInsights/bookmarks|
|Update Cases   |Microsoft.SecurityInsights/Cases|
|Update Case Investigation  |Microsoft.SecurityInsights/Cases|
|Create Case Comments   |Microsoft.SecurityInsights/Cases|
|Update Data Connectors |Microsoft.SecurityInsights/dataConnectors|
|Delete Data Connectors |Microsoft.SecurityInsights/dataConnectors|
|Update Settings    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>View audit and reporting data in Azure Sentinel

You can view this data by streaming it from the Azure Activity log into Azure Sentinel where you can then perform research and analytics on it.

1. Connect the [Azure Activity](connect-azure-activity.md) data source. After doing this, audit events are streamed into a new table in the **Logs** screen called AzureActivity.
2. Then, query the data using KQL, like you would any other table.



## <a name="vendor-documentation"></a>Vendor documentation

| **Fornecedor**  | **Use incident in Azure Sentinel** | **Ligação**|
|----|----|----|
| GitHub| Used to access Community page| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configure CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto Query Language course| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs and forums

Post your questions on the [TechCommunity space](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) for Azure Sentinel.

View Azure Sentinel blog posts from the [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) and [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Passos seguintes
In this document, you got a list of resources that are useful when you're working with Azure Sentinel. You'll find additional information about Azure security and compliance on the [Microsoft Azure Security and Compliance blog](https://blogs.msdn.com/b/azuresecurity/).
