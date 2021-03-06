---
title: Monitor e sintonização - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve funcionalidades de monitorização e afinação na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 8c4479d0892a8a6d5f613eff4592cca93e9fb179
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605132"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitore e sintonize a base de dados de Azure para PostgreSQL - Servidor Único
Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Azure Database for PostgreSQL fornece várias opções de monitorização para fornecer informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
A Azure Database for PostgreSQL fornece várias métricas que dão uma visão do comportamento dos recursos que suportam o servidor PostgreSQL. Cada métrica é emitida com uma frequência de um minuto, e tem até [93 dias de história.](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics) Pode configurar alertas sobre as métricas. Para orientação passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para Azure Database para PostgreSQL:

|Metric|Nome de exibição métrica|Unidade|Description|
|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|A percentagem de CPU em uso.|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|io_consumption_percent|IO por cento|Percentagem|A percentagem de IO em uso. (Não aplicável aos servidores de nível básico.)|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|serverlog_storage_percent|Por cento de armazenamento de registo de servidor|Percentagem|A percentagem de armazenamento de registo de servidor utilizado fora do armazenamento máximo de registo do servidor do servidor.|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|A quantidade de armazenamento de registo de servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento de registo de servidor|Bytes|O armazenamento máximo de registo de registo do servidor para este servidor.|
|active_connections|Ligações Ativas|de palavras|O número de ligações ativas ao servidor.|
|connections_failed|Ligações com Falhas|de palavras|O número de ligações estabelecidas que falharam.|
|network_bytes_egress|Saída da Rede|Bytes|Rede para fora através de ligações ativas.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em através de ligações ativas.|
|backup_storage_used|Armazenamento de backup usado|Bytes|A quantidade de armazenamento de reserva usado. Esta métrica representa a soma de armazenamento consumida por todas as cópias de segurança completas da base de dados, cópias de segurança diferenciais e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. A frequência das cópias de segurança é gerida e explicada no [artigo de conceitos](concepts-backup.md). Para o armazenamento geo-redundante, o uso de armazenamento de backup é o dobro do armazenamento localmente redundante.|
|pg_replica_log_delay_in_bytes|Max Lag através de réplicas|Bytes|O atraso nos bytes entre o primário e a réplica mais atrasada. Esta métrica está disponível apenas no servidor primário.|
|pg_replica_log_delay_in_seconds|Lag de réplica|Segundos|O tempo desde a última transação reproduzida. Esta métrica está disponível apenas para servidores de réplicas.|

## <a name="server-logs"></a>Registos do servidor
Pode ativar o início de sessão no seu servidor. Estes registos de recursos podem ser enviados para [registos do Monitor Azure,](../azure-monitor/logs/log-query-overview.md)Centros de Eventos e uma Conta de Armazenamento. Para saber mais sobre o registo de [registos,](concepts-server-logs.md) visite a página de registos do servidor.

## <a name="query-store"></a>Arquivo de Consultas
[A Loja de Consultas](concepts-query-store.md) mantém o registo do desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de consulta e eventos de espera. A funcionalidade persiste em consulta de informações de desempenho de tempo de execução numa base de dados do sistema chamada **azure_sys** sob o esquema query_store. Pode controlar a recolha e armazenamento de dados através de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight
[A Consulta Performance Insight](concepts-query-performance-insight.md) funciona em conjunto com a Query Store para fornecer visualizações acessíveis a partir do portal Azure. Estes gráficos permitem identificar as principais consultas que impactam o desempenho. O Insight de Desempenho de Consulta está acessível a partir da secção de resolução de **problemas do Suporte +** da sua Base de Dados de Azure para a página do portal do servidor PostgreSQL.

## <a name="performance-recommendations"></a>Recomendações de Desempenho
O recurso ['Recomendações de Desempenho'](concepts-performance-recommendations.md) identifica oportunidades para melhorar o desempenho da carga de trabalho. As Recomendações de Desempenho fornecem-lhe recomendações para a criação de novos índices que tenham o potencial de melhorar o desempenho das suas cargas de trabalho. Para produzir recomendações de índice, a funcionalidade tem em conta várias características da base de dados, incluindo o seu esquema e a carga de trabalho reportada pela Query Store. Após a implementação de qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planeada

[As notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) permitem-lhe receber alertas para a próxima manutenção planeada na sua Base de Dados Azure para PostgreSQL - Servidor Único. Estas notificações estão integradas na manutenção planeada [do Service Health](../service-health/overview.md) e permitem-lhe visualizar todas as manutenção programadas para as suas subscrições num só local. Também ajuda a escalar a notificação para o público certo para diferentes grupos de recursos, pois você pode ter diferentes contactos responsáveis por diferentes recursos. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

Saiba mais sobre como configurar notificações no documento [de notificações de manutenção planeado.](./concepts-planned-maintenance-notification.md)

## <a name="next-steps"></a>Passos seguintes
- Veja [como configurar alertas](howto-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
- Para obter mais informações sobre como aceder e exportar métricas utilizando o portal Azure, REST API ou CLI, consulte a Visão Geral das [Métricas Azure](../azure-monitor/data-platform.md)
- Leia o nosso blog sobre [as melhores práticas para monitorizar o seu servidor.](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)
- Saiba mais sobre [as notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) na Base de Dados Azure para PostgreSQL - Single Server.