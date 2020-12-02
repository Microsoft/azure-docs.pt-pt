---
title: Monitorização - Base de Dados Azure para MariaDB
description: Este artigo descreve as métricas de monitorização e alerta para a Base de Dados Azure para a MariaDB, incluindo CPU, armazenamento e estatísticas de conexão.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 7420a7c6355fbcd70ce57fbb36ab29d1241df31f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453332"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorização na Base de Dados Azure para MariaDB
Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Azure Database for MariaDB fornece várias métricas que dão uma visão do comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../azure-monitor/platform/data-platform.md).

Para orientação passo a passo, consulte [como configurar alertas](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para Azure Database for MariaDB:

|Metric|Nome de exibição métrica|Unidade|Descrição|
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

Pode ativar o início de sessão de consulta lenta no seu servidor. Estes registos também estão disponíveis através de Registos de Diagnóstico Azure em registos do Monitor Azure, Centros de Eventos e Conta de Armazenamento. Para saber mais sobre o registo de [registos,](concepts-server-logs.md) visite a página de registos do servidor.

## <a name="query-store"></a>Arquivo de Consultas

[A Loja de Consultas](concepts-query-store.md) mantém o registo do desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de consulta e eventos de espera. A funcionalidade persiste em consulta de informações de desempenho de tempo de execução no esquema **mysql.** Pode controlar a recolha e armazenamento de dados através de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight

[A Consulta Performance Insight](concepts-query-performance-insight.md) funciona em conjunto com a Query Store para fornecer visualizações acessíveis a partir do portal Azure. Estes gráficos permitem identificar as principais consultas que impactam o desempenho. O Insight de Desempenho de Consulta está acessível na secção **de Desempenho Inteligente** da sua Base de Dados Azure para a página do portal do servidor MariaDB.

## <a name="performance-recommendations"></a>Recomendações de Desempenho

O recurso ['Recomendações de Desempenho'](concepts-performance-recommendations.md) identifica oportunidades para melhorar o desempenho da carga de trabalho. As Recomendações de Desempenho fornecem-lhe recomendações para a criação de novos índices que tenham o potencial de melhorar o desempenho das suas cargas de trabalho. Para produzir recomendações de índice, a funcionalidade tem em conta várias características da base de dados, incluindo o seu esquema e a carga de trabalho reportada pela Query Store. Após a implementação de qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planeada

[As notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) permitem-lhe receber alertas para a próxima manutenção planeada na sua Base de Dados Azure para a MariaDB. Estas notificações estão integradas na manutenção planeada [do Service Health](../service-health/overview.md) e permitem-lhe visualizar todas as manutenção programadas para as suas subscrições num só local. Também ajuda a escalar a notificação para o público certo para diferentes grupos de recursos, pois você pode ter diferentes contactos responsáveis por diferentes recursos. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

Saiba mais sobre como configurar notificações no documento [de notificações de manutenção planeado.](./concepts-planned-maintenance-notification.md)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como aceder e exportar métricas utilizando o portal Azure, REST API ou CLI, consulte a Visão Geral das [Métricas Azure](../azure-monitor/platform/data-platform.md).
- Veja [como configurar alertas](howto-alert-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
- Saiba mais sobre [as notificações de manutenção planeadas](./concepts-planned-maintenance-notification.md) na Base de Dados Azure para MariaDB.