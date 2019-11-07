---
title: Monitoramento no banco de dados do Azure para MySQL
description: Este artigo descreve as métricas para monitoramento e alertas para o banco de dados do Azure para MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 920d632dfa15c25905dc4077465e6f8654b5f57d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603174"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoramento no banco de dados do Azure para MySQL
O monitoramento de dados sobre seus servidores ajuda você a solucionar problemas e otimizar sua carga de trabalho. O banco de dados do Azure para MySQL fornece várias métricas que fornecem informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada métrica fornece 30 dias de histórico. Você pode configurar alertas nas métricas. Para obter orientações passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem a configuração de ações automatizadas, a execução de análises avançadas e o histórico de arquivamento. Para obter mais informações, consulte a [visão geral de métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o banco de dados do Azure para MySQL:

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|A porcentagem de CPU em uso.|
|memory_percent|Porcentagem de memória|Percentagem|A porcentagem de memória em uso.|
|io_consumption_percent|Porcentagem de e/s|Percentagem|A porcentagem de e/s em uso.|
|storage_percent|Porcentagem de armazenamento|Percentagem|A porcentagem de armazenamento usada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transações e os logs do servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|A porcentagem de armazenamento de log do servidor usado fora do armazenamento de log do servidor máximo do servidor.|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|O armazenamento máximo de log do servidor para este servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Ligações com Falhas|Contagem|O número de conexões com falha com o servidor.|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|O número de segundos que o servidor de réplica está esdefasando no servidor mestre.|
|network_bytes_egress|Saída da Rede|Bytes|Saída de rede entre conexões ativas.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede entre conexões ativas.|
|backup_storage_used|Armazenamento de backup usado|Bytes|A quantidade de armazenamento de backup usada.|

## <a name="server-logs"></a>Registos do servidor
Você pode habilitar a consulta lenta e o log de auditoria em seu servidor. Esses logs também estão disponíveis por meio dos logs de diagnóstico do Azure em logs de Azure Monitor, hubs de eventos e conta de armazenamento. Para saber mais sobre registro em log, visite os artigos [logs de auditoria](concepts-audit-logs.md) e [logs de consulta lentos](concepts-server-logs.md) .

## <a name="query-store"></a>Arquivo de Consultas
[Repositório de consultas](concepts-query-store.md) é um recurso que controla o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. O recurso persiste as informações de desempenho de tempo de execução de consulta no esquema **MySQL** . Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight
[Análise de desempenho de consultas](concepts-query-performance-insight.md) trabalha em conjunto com repositório de consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. Análise de Desempenho de Consultas é acessível na seção de **desempenho inteligente** da página do portal do banco de dados do Azure para o servidor MySQL.

## <a name="performance-recommendations"></a>Recomendações de Desempenho
O recurso de [recomendações de desempenho](concepts-performance-recommendations.md) identifica oportunidades para melhorar o desempenho da carga de trabalho. As recomendações de desempenho fornecem recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características de banco de dados, incluindo seu esquema e a carga de trabalho conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="next-steps"></a>Passos seguintes
- Consulte [como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o portal do Azure, a API REST ou a CLI, consulte a [visão geral das métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia nosso blog sobre [as práticas recomendadas para monitorar o servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
