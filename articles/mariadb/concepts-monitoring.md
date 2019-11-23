---
title: Monitoring in Azure Database for MariaDB
description: This article describes the metrics for monitoring and alerting for Azure Database for MariaDB, including CPU, storage, and connection statistics.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3c25798be8af26c1f5e5c1178395cd1688bb132
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382053"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitoring in Azure Database for MariaDB
Monitoring data about your servers helps you troubleshoot and optimize for your workload. Azure Database for MariaDB provides various metrics that give insight into the behavior of your server.

## <a name="metrics"></a>Métricas
All Azure metrics have a one-minute frequency, and each metric provides 30 days of history. You can configure alerts on the metrics. Other tasks include setting up automated actions, performing advanced analytics, and archiving history. For more information, see the [Azure Metrics Overview](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

For step by step guidance, see [How to set up alerts](howto-alert-metric.md).

### <a name="list-of-metrics"></a>List of metrics
These metrics are available for Azure Database for MariaDB:

|Métrica|Metric Display Name|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|CPU percent|Percentagem|The percentage of CPU in use.|
|memory_percent|Memory percent|Percentagem|The percentage of memory in use.|
|io_consumption_percent|IO percent|Percentagem|The percentage of IO in use.|
|storage_percent|Storage percentage|Percentagem|The percentage of storage used out of the server's maximum.|
|storage_used|Armazenamento utilizado|Bytes|The amount of storage in use. The storage used by the service may include the database files, transaction logs, and the server logs.|
|serverlog_storage_percent|Server Log storage percent|Percentagem|The percentage of server log storage used out of the server's maximum server log storage.|
|serverlog_storage_usage|Server Log storage used|Bytes|The amount of server log storage in use.|
|serverlog_storage_limit|Server Log storage limit|Bytes|The maximum server log storage for this server.|
|storage_limit|Storage limit|Bytes|The maximum storage for this server.|
|active_connections|Active Connections|Contagem|The number of active connections to the server.|
|connections_failed|Ligações com Falhas|Contagem|The number of failed connections to the server.|
|network_bytes_egress|Saída da Rede|Bytes|Network Out across active connections.|
|network_bytes_ingress|Entrada na Rede|Bytes|Network In across active connections.|

## <a name="server-logs"></a>Registos do servidor

You can enable slow query logging on your server. These logs are also available through Azure Diagnostic Logs in Azure Monitor logs, Event Hubs, and Storage Account. To learn more about logging, visit the [server logs](concepts-server-logs.md) page.

## <a name="query-store"></a>Arquivo de Consultas

[Query Store](concepts-query-store.md) keeps track of query performance over time including query runtime statistics and wait events. The feature persists query runtime performance information in the **mysql** schema. You can control the collection and storage of data via various configuration knobs.

## <a name="query-performance-insight"></a>Estatísticas de Desempenho de Consultas

[Query Performance Insight](concepts-query-performance-insight.md) works in conjunction with Query Store to provide visualizations accessible from the Azure portal. These charts enable you to identify key queries that impact performance. Query Performance Insight is accessible in the **Intelligent Performance** section of your Azure Database for MariaDB server's portal page.

## <a name="performance-recommendations"></a>Recomendações de Desempenho

The [Performance Recommendations](concepts-performance-recommendations.md) feature identifies opportunities to improve workload performance. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. To produce index recommendations, the feature takes into consideration various database characteristics, including its schema and the workload as reported by Query Store. After implementing any performance recommendation, customers should test performance to evaluate the impact of those changes.

## <a name="service-health"></a>Estado de funcionamento do serviço
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for MariaDB by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>Passos seguintes

- For more information on how to access and export metrics using the Azure portal, REST API, or CLI, see the [Azure Metrics Overview](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - See [How to set up alerts](howto-alert-metric.md) for guidance on creating an alert on a metric.
