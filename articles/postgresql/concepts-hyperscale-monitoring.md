---
title: Monitor e sintonização - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve funcionalidades de monitorização e afinação na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9a6d8843acf27a53e261d9c7180906b15d05cc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314815"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitore e afinar base de dados de Azure para PostgreSQL - Hiperescala (Citus)

Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Hiperescala (Citus) fornece várias opções de monitorização para fornecer informações sobre o comportamento dos nós num grupo de servidores.

## <a name="metrics"></a>Métricas

A hiperescala (Citus) fornece métricas para cada nó num grupo de servidores. As métricas dão uma visão do comportamento de apoio aos recursos. Cada métrica é emitida com uma frequência de um minuto, e tem até 30 dias de história.

Além de visualizar gráficos das métricas, pode configurar alertas. Para orientação passo a passo, consulte [como configurar alertas](howto-hyperscale-alert-on-metric.md).  Outras tarefas incluem a criação de ações automatizadas, a execução de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas

Estas métricas estão disponíveis para nós de Hiperescala (Citus):

|Métrica|Nome de exibição métrica|Unidade|Descrição|
|---|---|---|---|
|active_connections|Ligações Ativas|Contagem|O número de ligações ativas ao servidor.|
|cpu_percent|CPU por cento|Percentagem|A percentagem de CPU em uso.|
|iops|IOPS|Contagem|Consulte a [definição de IOPS](../virtual-machines/premium-storage-performance.md#iops) e [a produção de Hiperescala (Citus)](concepts-hyperscale-configuration-options.md)|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em através de ligações ativas.|
|network_bytes_egress|Saída da Rede|Bytes|Rede para fora através de ligações ativas.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento utilizada fora do máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|

O Azure não fornece métricas agregadas para o cluster como um todo, mas as métricas para múltiplos nós podem ser colocadas no mesmo gráfico.

## <a name="next-steps"></a>Passos seguintes

- Veja [como configurar alertas](howto-hyperscale-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
