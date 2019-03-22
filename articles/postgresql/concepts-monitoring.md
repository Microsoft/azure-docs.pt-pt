---
title: Monitorizar e otimizar na base de dados do Azure para PostgreSQL
description: Este artigo descreve a monitorização e otimização de recursos na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 0d5508fe6b07bd5664bd0708960559537d8f6be1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098496"
---
# <a name="monitor-and-tune"></a>Monitorizar e otimizar
Dados sobre os servidores de monitorização ajuda-o a resolver problemas e otimizar a sua carga de trabalho. Base de dados do Azure para PostgreSQL fornece várias opções de monitorização para fornecer informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Base de dados do Azure para PostgreSQL fornece várias métricas que lhe dar informações sobre o comportamento de recursos que suportam o servidor PostgreSQL. Cada métrica é emitida uma frequência de um minuto e tem até 30 dias do histórico. Pode configurar alertas sobre as métricas. Para obter orientações passo a passo, consulte [como posso configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem definir ações automatizadas, a realização de análises avançadas e o arquivamento de histórico. Para obter mais informações, consulte a [descrição geral das métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para a base de dados do Azure para PostgreSQL:

|Métrica|Nome a apresentar de métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|A percentagem de CPU em utilização.|
|memory_percent|Percentagem de memória|Percentagem|A percentagem de memória em utilização.|
|io_consumption_percent|Percentagem de e/s|Percentagem|A percentagem de e/s em utilização.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizado fora do servidor 's máxima.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em utilização. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e os registos do servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|A percentagem de armazenamento de registo de servidor utilizado fora do armazenamento de registos de servidor máxima do servidor.|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|A quantidade de armazenamento de registo do servidor em utilização.|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|O armazenamento de registo de servidor máxima para este servidor.|
|active_connections|Ligações ativas|Contagem|O número de ligações ativas para o servidor.|
|connections_failed|Ligações com Falhas|Contagem|O número de ligações falhadas para o servidor.|
|network_bytes_egress|Saída da Rede|Bytes|Rede horizontalmente através de ligações de Active Directory.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede no através de ligações de Active Directory.|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|A quantidade de armazenamento de cópia de segurança utilizado.|

## <a name="server-logs"></a>Registos do servidor
Pode ativar o registo no seu servidor. Estes registos também estão disponíveis através de registos de diagnóstico do Azure no [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md), os Hubs de eventos e a conta de armazenamento. Para saber mais sobre o registo, visite o [registos do servidor](concepts-server-logs.md) página.

## <a name="query-store"></a>Arquivo de Consultas
[Consulta Store](concepts-query-store.md) é uma funcionalidade de pré-visualização pública que mantém o controle da consulta de desempenho ao longo do tempo incluindo estatísticas de tempo de execução de consulta e eventos de espera. A funcionalidade de informações de desempenho de tempo de execução de consulta numa base de dados do sistema com o nome de persistir **azure_sys** sob o esquema de query_store. Pode controlar a coleção e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) funciona em conjunto com Store de consulta para fornecer visualizações acessíveis a partir do portal do Azure. Estes gráficos permitem identificar consultas principais que um impacto no desempenho. O Query Performance Insight está em pré-visualização pública e está acessível a **suporte + resolução de problemas** secção da base de dados do Azure para a página do portal do servidor PostgreSQL.

## <a name="performance-recommendations"></a>Recomendações de Desempenho
O [recomendações de desempenho](concepts-performance-recommendations.md) funcionalidade identifica oportunidades para melhorar o desempenho da carga de trabalho. A versão de pré-visualização pública de recomendações de desempenho fornece recomendações para a criação de índices novo que têm o potencial de melhorar o desempenho das suas cargas de trabalho. Para produzir recomendações de índice, a funcionalidade leva em consideração várias características de base de dados, incluindo o respetivo esquema e a carga de trabalho, conforme comunicado pelo Store de consulta. Depois de implementar quaisquer recomendações de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="next-steps"></a>Passos Seguintes
- Ver [como posso configurar alertas](howto-alert-on-metric.md) para obter orientações sobre como criar um alerta na métrica.
- Para obter mais informações sobre como aceder e exportar métricas com o portal do Azure, a REST API ou a CLI, consulte a [descrição geral das métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia o nosso blogue [melhores práticas para monitorização do servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
