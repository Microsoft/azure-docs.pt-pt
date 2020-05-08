---
title: Monitor e sintonização - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve funcionalidades de monitorização e afinação na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fb24100eaebc54b98adf2f09fa3e557e4d6fa0be
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580650"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitor e sintonize base de dados Azure para PostgreSQL - Hiperescala (Citus)

Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar para a sua carga de trabalho. A Hyperscale (Citus) fornece várias opções de monitorização para fornecer informações sobre o comportamento dos nós num grupo de servidores.

## <a name="metrics"></a>Métricas

A hiperescala (Citus) fornece métricas para cada nó num grupo de servidores. As métricas dão uma visão do comportamento dos recursos de apoio. Cada métrica é emitida com uma frequência de um minuto, e tem até 30 dias de história.

Além de visualizar gráficos das métricas, pode configurar alertas. Para orientação passo a passo, consulte [como configurar alertas](howto-hyperscale-alert-on-metric.md).  Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e o histórico de arquivamento. Para mais informações, consulte a visão geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas

Estas métricas estão disponíveis para nós de hiperescala (Citus):

|Métrica|Nome de exibição métrica|Unidade|Descrição|
|---|---|---|---|
|active_connections|Conexões Ativas|Contagem|O número de ligações ativas ao servidor.|
|cpu_percent|Por cento do CPU|Percentagem|A percentagem de CPU em uso.|
|iops|IOPS|Contagem|Consulte a [definição iOPS](../virtual-machines/linux/premium-storage-performance.md#iops) e a entrada em [hiperescala](concepts-hyperscale-configuration-options.md)|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em todas as ligações ativas.|
|network_bytes_egress|Saída da Rede|Bytes|Rede Para fora através de conexões ativas.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento usado no máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros da base de dados, registos de transações e os registos do servidor.|

O Azure não fornece métricas agregadas para o cluster como um todo, mas as métricas para vários nós podem ser colocadas no mesmo gráfico.

## <a name="next-steps"></a>Passos seguintes

- Veja [como configurar alertas](howto-hyperscale-alert-on-metric.md) para orientação sobre a criação de um alerta numa métrica.
