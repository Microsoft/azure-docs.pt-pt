---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como o Load Balancer funciona e como usá-lo na nuvem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045393"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

O *balanceamento de carga* refere-se à distribuição uniforme da carga (tráfego de rede de entrada) em um grupo de recursos de back-end ou servidores. O Azure oferece uma [variedade de opções de balanceamento de carga](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) que você pode escolher de acordo com suas necessidades. Este documento aborda o Azure Load Balancer.

Azure Load Balancer opera na camada quatro do modelo OSI (interconexão de sistemas abertos). É o único ponto de contato para clientes. O balanceador de carga distribui novos fluxos de entrada que chegam às instâncias de front-end do Load Balancer para o pool de back-ends. Esses fluxos são de acordo com as regras de balanceamento de carga configuradas e as investigações de integridade. As instâncias do pool de back-end podem ser máquinas virtuais ou instâncias do Azure em um conjunto de dimensionamento de máquinas virtuais.


Um **[balanceador de carga público](./concepts-limitations.md#publicloadbalancer)** pode fornecer conexões de saída para VMs (máquinas virtuais) dentro de sua rede virtual. Essas conexões são realizadas por meio da tradução de seus endereços IP privados para endereços IP públicos. Os balanceadores de carga públicos são usados para balancear a carga do tráfego de Internet para suas VMs.

Um **[balanceador de carga interno (ou privado)](./concepts-limitations.md#internalloadbalancer)** é usado onde os IPs privados são necessários apenas no front-end. Os balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual. Um front-end do balanceador de carga pode ser acessado de uma rede local em um cenário híbrido.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Figura: balanceamento de aplicativos de várias camadas usando Load Balancer públicos e internos*

Para obter mais informações sobre os componentes individuais do Load Balancer, consulte [Azure Load Balancer componentes e limitações](./concepts-limitations.md)

>[!NOTE]
> A Microsoft recomenda [Standard Load Balancer](./load-balancer-standard-overview.md).
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Load Balancer e o SKU do endereço IP público devem corresponder quando você os usa com endereços IP públicos. Load Balancer e SKUs de IP público não são mutáveis.

## <a name="why-use-azure-load-balancer"></a>Por que usar Azure Load Balancer?
Com o Azure Load Balancer, você pode dimensionar seus aplicativos e criar serviços altamente disponíveis. O balanceador de carga dá suporte a cenários de entrada e saída. O balanceador de carga fornece baixa latência e alta taxa de transferência e escala até milhões de fluxos para todos os aplicativos TCP e UDP.

Os principais cenários que você pode realizar usando Azure Load Balancer incluem:

- Balancear a carga de tráfego **[interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** e **[externo](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** para máquinas virtuais do Azure.

- Aumente a disponibilidade distribuindo recursos **[dentro](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** e **[entre](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zonas.

- Configure a **[conectividade de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** para máquinas virtuais do Azure.

- Use **[investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** para monitorar recursos de balanceamento de carga.

- Empregue o **[encaminhamento de porta](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** para acessar máquinas virtuais em uma rede virtual por endereço IP público e porta.

- Habilite o suporte para **[balanceamento de carga](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** do **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Aproveite as **[métricas e os diagnósticos](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** para Azure Load Balancer com **[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Balancear a carga de serviços em **[várias portas, vários endereços IP ou ambos](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Mova recursos de balanceador de carga **[internos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** e **[externos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** entre regiões do Azure.

- Balancear a carga do fluxo TCP e UDP em todas as portas simultaneamente usando **[portas de alta disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Preços

O uso de Standard Load Balancer é cobrado.

* Número de regras de balanceamento de carga e de saída configuradas. As regras de NAT de entrada não contam no número total de regras.
* Quantidade de dados processados de entrada e saída independentes de regras.

Para Standard Load Balancer informações sobre preços, consulte [preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

O Balanceador de Carga Básico é disponibilizado sem custos.

## <a name="sla"></a>SLA

Para obter informações sobre o SLA de Standard Load Balancer, consulte [SLA para Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Passos seguintes

Consulte [criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer.

Para obter mais informações sobre limitações e componentes do Azure Load Balancer, consulte [Azure Load Balancer conceitos e limitações](./concepts-limitations.md)
