---
title: Monitorização na base de dados do Azure para MySQL
description: Este artigo descreve as métricas de monitorização e alertas para base de dados do Azure para MySQL, incluindo as estatísticas de CPU, armazenamento e conexão.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 9dcb79e7f4ebd43da3f6c6fd35fa0707898d7ec8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111009"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorização na base de dados do Azure para MySQL
Dados sobre os servidores de monitorização ajuda-o a resolver problemas e otimizar a sua carga de trabalho. Base de dados do Azure para MySQL fornece várias métricas que lhe dar informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada métrica oferece 30 dias do histórico. Pode configurar alertas sobre as métricas. Para obter orientações passo a passo, consulte [como posso configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem definir ações automatizadas, a realização de análises avançadas e o arquivamento de histórico. Para obter mais informações, consulte a [descrição geral das métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para a base de dados do Azure para MySQL:

|Métrica|Nome a apresentar de métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|A percentagem de CPU em utilização.|
|memory_percent|Percentagem de memória|Percentagem|A percentagem de memória em utilização.|
|io_consumption_percent|Percentagem de e/s|Percentagem|A percentagem de e/s em utilização.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizado fora do servidor 's máxima.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em utilização. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e os registos do servidor.|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|A percentagem de armazenamento de registo de servidor utilizado fora do armazenamento de registos de servidor máxima do servidor.|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|A quantidade de armazenamento de registo do servidor em utilização.|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|O armazenamento de registo de servidor máxima para este servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|active_connections|Ligações ativas|Contagem|O número de ligações ativas para o servidor.|
|connections_failed|Ligações com Falhas|Contagem|O número de ligações falhadas para o servidor.|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|O número de segundos que o servidor de réplica é lagging contra o servidor mestre.|
|network_bytes_egress|Saída da Rede|Bytes|Rede horizontalmente através de ligações de Active Directory.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede no através de ligações de Active Directory.|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|A quantidade de armazenamento de cópia de segurança utilizado.|

## <a name="server-logs"></a>Registos do servidor
Pode ativar o registo no seu servidor de consulta lenta. Estes registos também estão disponíveis através de registos de diagnóstico do Azure em registos, os Hubs de eventos e conta de armazenamento do Azure Monitor. Para saber mais sobre o registo, visite o [registos do servidor](concepts-server-logs.md) página.

## <a name="next-steps"></a>Passos Seguintes
- Ver [como posso configurar alertas](howto-alert-on-metric.md) para obter orientações sobre como criar um alerta na métrica.
- Para obter mais informações sobre como aceder e exportar métricas com o portal do Azure, a REST API ou a CLI, consulte a [descrição geral das métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia o nosso blogue [melhores práticas para monitorização do servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
