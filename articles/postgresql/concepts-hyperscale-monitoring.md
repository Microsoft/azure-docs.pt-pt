---
title: Monitorar e ajustar no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Este artigo descreve os recursos de monitoramento e ajuste no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c9bf7a427c0d3d26c3accd1d0d7098413916993
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512694"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorar e ajustar o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

O monitoramento de dados sobre seus servidores ajuda você a solucionar problemas e otimizar sua carga de trabalho. O Citus (hiperescala) fornece várias opções de monitoramento para fornecer informações sobre o comportamento de nós em um grupo de servidores.

## <a name="metrics"></a>Métricas

O Citus (hiperescala) fornece métricas para cada nó em um grupo de servidores. As métricas fornecem informações sobre o comportamento dos recursos de suporte. Cada métrica é emitida a uma frequência de um minuto e tem até 30 dias de histórico.

Além de exibir grafos das métricas, você pode configurar alertas. Para obter orientações passo a passo, consulte [como configurar alertas](howto-hyperscale-alert-on-metric.md).  Outras tarefas incluem a configuração de ações automatizadas, a execução de análises avançadas e o histórico de arquivamento. Para obter mais informações, consulte a [visão geral de métricas do Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas

Essas métricas estão disponíveis para nós de hiperescala (Citus):

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|cpu_percent|Percentual de CPU|Percentagem|A porcentagem de CPU em uso.|
|IOPS|IOPS|Contagem|Consulte a [definição de IOPS](../virtual-machines/linux/premium-storage-performance.md#iops) e a [taxa de transferência de hiperescala](concepts-hyperscale-configuration-options.md)|
|memory_percent|Porcentagem de memória|Percentagem|A porcentagem de memória em uso.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede entre conexões ativas.|
|network_bytes_egress|Saída da Rede|Bytes|Saída de rede entre conexões ativas.|
|storage_percent|Porcentagem de armazenamento|Percentagem|A porcentagem de armazenamento usada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transações e os logs do servidor.|

O Azure não fornece métricas agregadas para o cluster como um todo, mas as métricas para vários nós podem ser colocadas no mesmo grafo.

## <a name="next-steps"></a>Passos seguintes

- Consulte [como configurar alertas](howto-hyperscale-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
