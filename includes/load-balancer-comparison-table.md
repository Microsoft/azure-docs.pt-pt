---
title: incluir ficheiro
description: incluir ficheiro
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78202503"
---
| | Balanceador de Carga Padrão | Balanceador de Carga Básico |
| --- | --- | --- |
| [Tamanho da piscina de backend](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Suporta até 1000 casos. | Suporta até 300 instâncias. |
| Pontos finais do pool backend | Quaisquer máquinas virtuais ou conjuntos de escala de máquinavirtual numa única rede virtual. | Máquinas virtuais num único conjunto de disponibilidade ou conjunto de escala de máquina virtual. |
| [Sondas do estado de funcionamento](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Saúde sonda para baixo comportamento](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As ligações TCP permanecem vivas numa sonda de exemplo para baixo __e__ em todas as sondas para baixo. | As ligações TCP permanecem vivas numa sonda de exemplo. Todas as ligações TCP terminam quando todas as sondas estão em baixo. |
| Zonas de Disponibilidade | Extremidades de zona redundante e zonal para tráfego de entrada e saída. | Não disponível |
| Diagnóstico | [Métricas multidimensionais do Monitor Azure](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Registos do Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Portos HA | [Disponível para Balancer de Carga Interna](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Não disponível |
| Seguro por padrão | Fechado a fluxos de entrada a menos que seja permitido por um grupo de segurança da rede. Por favor, note que o tráfego interno do VNet para o equilibrador de carga interno é permitido. | Aberto por defeito. Grupo de segurança de rede opcional. |
| Regras de Saída | [Configuração nat de saída declarativa](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Não disponível |
| Reset TCP em Idle | [Disponível em qualquer regra](../articles/load-balancer/load-balancer-tcp-reset.md) | Não disponível |
| [Múltiplas extremidades dianteiras](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Apenas a entrada |
| Operações de Gestão | A maioria das operações < 30 segundos | 60-90 segundos típicos |
| SLA | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Não disponível | 
