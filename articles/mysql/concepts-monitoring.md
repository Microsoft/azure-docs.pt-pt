---
title: Monitorização - Base de Dados Azure para MySQL
description: Este artigo descreve as métricas de monitorização e alerta para a Base de Dados Azure para o MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 4589687593abbe5667aa4b21b1361b68e4bed07f
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428126"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorização na Base de Dados Azure para o MySQL
Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Azure Database for MySQL fornece várias métricas que dão uma visão do comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. Para orientação passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para Azure Database para MySQL:

|Métrica|Nome de exibição métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|A percentagem de CPU em uso.|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|io_consumption_percent|IO por cento|Percentagem|A percentagem de IO em uso. (Não aplicável aos servidores de nível básico)|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|
|serverlog_storage_percent|Por cento de armazenamento de registo de servidor|Percentagem|A percentagem de armazenamento de registo de servidor utilizado fora do armazenamento máximo de registo do servidor do servidor.|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|A quantidade de armazenamento de registo de servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento de registo de servidor|Bytes|O armazenamento máximo de registo de registo do servidor para este servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|active_connections|Ligações Ativas|de palavras|O número de ligações ativas ao servidor.|
|connections_failed|Ligações com Falhas|de palavras|O número de ligações falhadas ao servidor.|
|seconds_behind_master|Atraso de replicação em segundos|de palavras|O número de segundos em que o servidor de réplica está a atrasar-se contra o servidor de origem. (Não aplicável aos servidores de nível básico)|
|network_bytes_egress|Saída da Rede|Bytes|Rede para fora através de ligações ativas.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em através de ligações ativas.|
|backup_storage_used|Armazenamento de backup usado|Bytes|A quantidade de armazenamento de reserva usado. Esta métrica representa a soma de armazenamento consumida por todas as cópias de segurança completas da base de dados, cópias de segurança diferenciais e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. A frequência das cópias de segurança é gerida e explicada no [artigo de conceitos](concepts-backup.md). Para o armazenamento geo-redundante, o uso de armazenamento de backup é o dobro do armazenamento localmente redundante.|

## <a name="server-logs"></a>Registos do servidor
Pode ativar consultas lentas e fazer login de auditoria no seu servidor. Estes registos também estão disponíveis através de Registos de Diagnóstico Azure em registos do Monitor Azure, Centros de Eventos e Conta de Armazenamento. Para saber mais sobre o registo madeireiros, visite os [registos](concepts-audit-logs.md) de auditoria e [artigos de registos de consultas lentas.](concepts-server-logs.md)

## <a name="query-store"></a>Arquivo de Consultas
[A Consulta Store](concepts-query-store.md) é uma funcionalidade que acompanha o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de consulta e eventos de espera. A funcionalidade persiste em consulta de informações de desempenho de tempo de execução no esquema **mysql.** Pode controlar a recolha e armazenamento de dados através de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight
[A Consulta Performance Insight](concepts-query-performance-insight.md) funciona em conjunto com a Query Store para fornecer visualizações acessíveis a partir do portal Azure. Estes gráficos permitem identificar as principais consultas que impactam o desempenho. O Insight de Desempenho de Consulta está acessível na secção **de Desempenho Inteligente** da sua Base de Dados Azure para a página do portal do servidor MySQL.

## <a name="performance-recommendations"></a>Recomendações de Desempenho
O recurso ['Recomendações de Desempenho'](concepts-performance-recommendations.md) identifica oportunidades para melhorar o desempenho da carga de trabalho. As Recomendações de Desempenho fornecem-lhe recomendações para a criação de novos índices que tenham o potencial de melhorar o desempenho das suas cargas de trabalho. Para produzir recomendações de índice, a funcionalidade tem em conta várias características da base de dados, incluindo o seu esquema e a carga de trabalho reportada pela Query Store. Após a implementação de qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planeada

[As notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) permitem-lhe receber alertas para a próxima manutenção planeada na sua Base de Dados Azure para o MySQL. Estas notificações estão integradas na manutenção planeada [do Service Health](../service-health/overview.md) e permitem-lhe visualizar todas as manutenção programadas para as suas subscrições num só local. Também ajuda a escalar a notificação para o público certo para diferentes grupos de recursos, pois você pode ter diferentes contactos responsáveis por diferentes recursos. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

Saiba mais sobre como configurar notificações no documento [de notificações de manutenção planeado.](./concepts-planned-maintenance-notification.md)

## <a name="next-steps"></a>Passos seguintes
- Veja [como configurar alertas](howto-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
- Para obter mais informações sobre como aceder e exportar métricas utilizando o portal Azure, REST API ou CLI, consulte a Visão Geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia o nosso blog sobre [as melhores práticas para monitorizar o seu servidor.](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)
- Saiba mais sobre [notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) na Base de Dados Azure para MySQL - Servidor Único
