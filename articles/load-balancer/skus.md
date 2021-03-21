---
title: SKUs de balançador de carga Azure
description: Visão geral dos SKUs do balançador de carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698092"
---
# <a name="azure-load-balancer-skus"></a>SKUs de balançador de carga Azure

O Azure Load Balancer tem dois SKUs.

## <a name="sku-comparison"></a><a name="skus"></a> Comparação SKU

O equilibrador de carga suporta skus standard e básico. Estes SKUs diferem na escala de cenário, características e preços. Qualquer cenário possível com o balanceador de carga Básico pode ser criado com o balanceador de carga Standard.

Para comparar e compreender as diferenças, veja a tabela seguinte. Para obter mais informações, consulte [a visão geral do Balanço de Carga Padrão Azure](./load-balancer-overview.md).

>[!NOTE]
> A Microsoft recomenda o balanceador de carga Standard.
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. O balançador de carga e o endereço IP público SKU devem coincidir quando os utilizar com endereços IP públicos. O balanceador de carga e os SKUs ip públicos não são mutáveis.

| | Balanceador de carga padrão | Balanceador de carga básico |
| --- | --- | --- |
| **[Tamanho da piscina backend](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Suporta até 1000 casos. | Suporta até 300 casos. |
| **Backend pool endpoints** | Qualquer máquina virtual ou conjuntos de escala de máquina virtual numa única rede virtual. | Máquinas virtuais num conjunto de disponibilidade única ou conjunto de escala de máquina virtual. |
| **[Sondas do estado de funcionamento](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Saúde sonda para baixo comportamento](./load-balancer-custom-probe-overview.md#probedown)** | As ligações TCP permanecem vivas numa sonda de instância para baixo __e__ em todas as sondas para baixo. | As ligações da TCP permanecem vivas numa sonda de exemplo. Todas as ligações TCP terminam quando todas as sondas estão em baixo. |
| **Zonas de Disponibilidade** | Frontends zona redundantes e zonais para tráfego de entrada e saída. | Não disponível |
| **Diagnóstico** | [Métricas multidimensionais do Monitor Azure](./load-balancer-standard-diagnostics.md) | [Registos do Azure Monitor](./load-balancer-monitor-log.md) |
| **Portos HA** | [Disponível para equilibrador de carga interna](./load-balancer-ha-ports-overview.md) | Não disponível |
| **Proteger por defeito** | Fechado a fluxos de entrada, a menos que seja permitido por um grupo de segurança de rede. É permitido tráfego interno da rede virtual para o balançador de carga interno. | Abrir por defeito. Grupo de segurança de rede opcional. |
| **Regras de saída** | [Configuração DE NAT de saída declarativa](./load-balancer-outbound-connections.md#outboundrules) | Não disponível |
| **Reposição de TCP em Idle** | [Disponível em qualquer regra](./load-balancer-tcp-reset.md) | Não disponível |
| **[Múltiplas extremidades dianteiras](./load-balancer-multivip-overview.md)** | Entrada e [saída](./load-balancer-outbound-connections.md) | Só de entrada |
| **Operações de Gestão** | A maioria das operações < 30 segundos | 60-90+ segundos típicos |
| **SLA** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Não disponível | 

Para obter mais informações, consulte [os limites do balançador de carga](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](./load-balancer-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

- Os SKUs não são mutáveis. Não se pode mudar o SKU de um recurso existente.
- Um recurso de máquina virtual autónomo, um recurso de definição de disponibilidade ou um recurso de escala de máquina virtual podem referenciar um SKU, nunca ambos.
- [Operações de movimentação:](../azure-resource-manager/management/move-resource-group-and-subscription.md)
  - As operações de movimento do grupo de recursos (dentro da mesma subscrição) **são suportadas** para o Balancer de Carga Padrão e IP Público Padrão. 
  - [As operações de movimento do grupo de subscrição](../azure-resource-manager/management/move-support-resources.md) **não** são suportadas para os recursos Standard Load Balancer e Standard Public IP.

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um balanceador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com um balanceador de carga.
- Saiba como utilizar [o Balancer de Carga Padrão e as Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [as Sondas de Saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre a utilização [do Balanceador de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Balanceador de Carga Padrão com as regras de equilíbrio de carga dos portos HA](load-balancer-ha-ports-overview.md).
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/network-security-groups-overview.md)