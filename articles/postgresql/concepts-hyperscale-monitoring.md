---
title: Monitor e sintonização - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve funcionalidades de monitorização e afinação na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 544f871f62481243cda2409db24b0d067df28c32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580584"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitore e afinar base de dados de Azure para PostgreSQL - Hiperescala (Citus)

Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Hiperescala (Citus) fornece várias opções de monitorização para fornecer informações sobre o comportamento dos nós num grupo de servidores.

## <a name="metrics"></a>Métricas

A hiperescala (Citus) fornece métricas para cada nó num grupo de servidores. As métricas dão uma visão do comportamento de apoio aos recursos. Cada métrica é emitida com uma frequência de um minuto, e tem até 30 dias de história.

Além de visualizar gráficos das métricas, pode configurar alertas. Para orientação passo a passo, consulte [como configurar alertas](howto-hyperscale-alert-on-metric.md).  Outras tarefas incluem a criação de ações automatizadas, a execução de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista de métricas

Estas métricas estão disponíveis para nós de Hiperescala (Citus):

|Metric|Nome de exibição métrica|Unidade|Description|
|---|---|---|---|
|active_connections|Ligações Ativas|de palavras|O número de ligações ativas ao servidor.|
|cpu_percent|CPU por cento|Percentagem|A percentagem de CPU em uso.|
|iops|IOPS|de palavras|Consulte a [definição de IOPS](../virtual-machines/premium-storage-performance.md#iops) e [a produção de Hiperescala (Citus)](concepts-hyperscale-configuration-options.md)|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em através de ligações ativas.|
|network_bytes_egress|Saída da Rede|Bytes|Rede para fora através de ligações ativas.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|

O Azure não fornece métricas agregadas para o cluster como um todo, mas as métricas para múltiplos nós podem ser colocadas no mesmo gráfico.

## <a name="next-steps"></a>Passos seguintes

- Veja [como configurar alertas](howto-hyperscale-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.