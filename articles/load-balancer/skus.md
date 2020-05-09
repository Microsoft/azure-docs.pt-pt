---
title: Equilíbrio de carga azure SKUs
description: Visão geral do Equilíbrio de Carga Azure SKUs
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: faf77411abca63516b00ac953bc7203da69f3ca9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854083"
---
# <a name="azure-load-balancer-skus"></a>Equilíbrio de carga azure SKUs

O Equilíbrio de Carga Azure tem dois sabores ou SKUs.

## <a name="sku-comparison"></a><a name="skus"></a>Comparação SKU

O equilibrador de carga suporta as SKUs Básicas e Standard. Estas SKUs diferem na escala de cenários, funcionalidades e preços. Qualquer cenário que seja possível com o equilíbrio de carga Basic pode ser criado com o equilibrador de carga Standard.

Para comparar e compreender as diferenças, veja a tabela seguinte. Para mais informações, consulte a visão geral do Equilíbrio de [Carga Padrão Azure](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft recomenda um equilíbrio de carga Standard.
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. O equilibrador de carga e o endereço IP público SKU devem coincidir quando os utilizar com endereços IP públicos. O equilibrador de carga e as IP SKUs públicas não são mutáveis.

| | Balanceador de Carga Padrão | Balanceador de Carga Básico |
| --- | --- | --- |
| [Tamanho da piscina de backend](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Suporta até 1000 casos. | Suporta até 300 instâncias. |
| Pontos finais do pool backend | Quaisquer máquinas virtuais ou conjuntos de escala de máquinavirtual numa única rede virtual. | Máquinas virtuais num único conjunto de disponibilidade ou conjunto de escala de máquina virtual. |
| [Sondas do estado de funcionamento](./load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Saúde sonda para baixo comportamento](./load-balancer-custom-probe-overview.md#probedown) | As ligações TCP permanecem vivas numa sonda de exemplo para baixo __e__ em todas as sondas para baixo. | As ligações TCP permanecem vivas numa sonda de exemplo. Todas as ligações TCP terminam quando todas as sondas estão em baixo. |
| Zonas de Disponibilidade | Extremidades de zona redundante e zonal para tráfego de entrada e saída. | Não disponível |
| Diagnóstico | [Métricas multidimensionais do Monitor Azure](./load-balancer-standard-diagnostics.md) | [Registos do Azure Monitor](./load-balancer-monitor-log.md) |
| Portos HA | [Disponível para Balancer de Carga Interna](./load-balancer-ha-ports-overview.md) | Não disponível |
| Seguro por padrão | Fechado a fluxos de entrada a menos que seja permitido por um grupo de segurança da rede. Por favor, note que o tráfego interno do VNet para o equilibrador de carga interno é permitido. | Aberto por defeito. Grupo de segurança de rede opcional. |
| Regras de Saída | [Configuração nat de saída declarativa](./load-balancer-outbound-rules-overview.md) | Não disponível |
| Reset TCP em Idle | [Disponível em qualquer regra](./load-balancer-tcp-reset.md) | Não disponível |
| [Múltiplas extremidades dianteiras](./load-balancer-multivip-overview.md) | Entrada e [saída](./load-balancer-outbound-connections.md) | Apenas a entrada |
| Operações de Gestão | A maioria das operações < 30 segundos | 60-90 segundos típicos |
| SLA | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Não disponível | 

Para obter mais informações, consulte [os limites do equilíbrio de carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

- As SKUs não são mutáveis. Não se pode mudar o SKU de um recurso existente.
- Um recurso de máquina virtual autónomo, recurso definido de disponibilidade ou recurso conjunto de escala de máquina virtual pode fazer referência a um SKU, nunca ambos.
- [As operações de subscrição de movimentos](../azure-resource-manager/management/move-resource-group-and-subscription.md) não são suportadas para os recursos standard load Balancer e Standard Public IP.

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com o uso de um Balancer de Carga.
- Saiba mais sobre a utilização de Zonas de [Equilíbrio de Carga Padrão e De Disponibilidade.](load-balancer-standard-availability-zones.md)
- Saiba mais sobre sondas de [saúde.](load-balancer-custom-probe-overview.md)
- Aprenda a utilizar o [Balancer de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre o [Balancer de Carga Standard com regras](load-balancer-ha-ports-overview.md)de equilíbrio de carga ha ports .
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)
