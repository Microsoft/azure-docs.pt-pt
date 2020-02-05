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
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 376741005f0755d01c95baad8d3a3d33e9952933
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023910"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

O *balanceamento de carga* refere-se à distribuição uniforme da carga (tráfego de rede de entrada) em um grupo de recursos de back-end ou servidores. 

Azure Load Balancer opera na camada quatro do modelo OSI (interconexão de sistemas abertos). É o único ponto de contato para clientes. Load Balancer distribui os fluxos de entrada que chegam às instâncias de front-end do balanceador de carga para o pool de back-end. Esses fluxos são de acordo com as regras de balanceamento de carga configuradas e as investigações de integridade. As instâncias do pool de back-end podem ser máquinas virtuais ou instâncias do Azure em um conjunto de dimensionamento de máquinas virtuais.

Um **[balanceador de carga público](./concepts-limitations.md#publicloadbalancer)** pode fornecer conexões de saída para VMs (máquinas virtuais) dentro de sua rede virtual. Essas conexões são realizadas por meio da tradução de seus endereços IP privados para endereços IP públicos. Os balanceadores de carga públicos são usados para balancear a carga do tráfego de Internet para suas VMs.

Um **[balanceador de carga interno (ou privado)](./concepts-limitations.md#internalloadbalancer)** é usado onde os IPs privados são necessários apenas no front-end. Os balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual. Um front-end do balanceador de carga pode ser acessado de uma rede local em um cenário híbrido.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Figura: balanceamento de aplicativos de várias camadas usando Load Balancer públicos e internos*

Para obter mais informações sobre os componentes individuais do Load Balancer, consulte [Azure Load Balancer componentes e limitações](./concepts-limitations.md)

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se precisa de alto desempenho, baixa latência, equilíbrio de carga camada 4, veja [o que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Se procura o equilíbrio global de carga slédi, veja o que é o Gestor de [Tráfego?](../traffic-manager/traffic-manager-overview.md) Os seus cenários de ponta a ponta podem beneficiar da combinação destas soluções.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-load-balancer"></a>Por que usar Azure Load Balancer?
Com o Standard Load Balancer, você pode dimensionar seus aplicativos e criar serviços altamente disponíveis. O balanceador de carga dá suporte a cenários de entrada e saída. O balanceador de carga fornece baixa latência e alta taxa de transferência e escala até milhões de fluxos para todos os aplicativos TCP e UDP.

Os principais cenários que você pode realizar usando Standard Load Balancer incluem:

- Balancear a carga de tráfego **[interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** e **[externo](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** para máquinas virtuais do Azure.

- Aumente a disponibilidade distribuindo recursos **[dentro](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** e **[entre](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zonas.

- Configure a **[conectividade de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** para máquinas virtuais do Azure.

- Use **[investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** para monitorar recursos de balanceamento de carga.

- Empregue o **[encaminhamento de porta](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** para acessar máquinas virtuais em uma rede virtual por endereço IP público e porta.

- Habilite o suporte para **[balanceamento de carga](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** do **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Standard Load Balancer fornece métricas multidimensionais por meio de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Essas métricas podem ser filtradas, agrupadas e divididas para uma determinada dimensão.  Eles fornecem informações atuais e históricas sobre o desempenho e a integridade do seu serviço.  Também há suporte para Resource Health. Examine **[Standard Load Balancer diagnóstico](load-balancer-standard-diagnostics.md)** para obter mais detalhes.

- Balancear a carga de serviços em **[várias portas, vários endereços IP ou ambos](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Mova recursos de balanceador de carga **[internos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** e **[externos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** entre regiões do Azure.

- Balancear a carga do fluxo TCP e UDP em todas as portas simultaneamente usando **[portas de alta disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

### <a name="securebydefault"></a>Seguro por padrão

O Standard Load Balancer se baseia no modelo de segurança de rede de confiança zero em seu núcleo. Standard Load Balancer seguro por padrão e faz parte de sua rede virtual. A rede virtual é uma rede privada e isolada.  Isso significa que os balanceadores de carga padrão e os endereços IP públicos padrão são fechados para fluxos de entrada, a menos que sejam abertos por grupos de segurança de rede. NSGs são usados para permitir explicitamente e o tráfego permitido pela lista de permissões.  Se você não tiver um NSG em uma sub-rede ou NIC de seu recurso de máquina virtual, o tráfego não terá permissão para acessar esse recurso. Para saber mais sobre o NSGs e como aplicá-los ao seu cenário, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).
Por padrão, o Load Balancer básico é aberto na Internet.


## <a name="pricing-and-sla"></a>Preços e SLA

Para Standard Load Balancer informações sobre preços, consulte [preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
O Balanceador de Carga Básico é disponibilizado sem custos.
Consulte [SLA para Load Balancer](https://aka.ms/lbsla). O Load Balancer básico não tem SLA.

## <a name="next-steps"></a>Passos seguintes

Consulte [criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer.

Para obter mais informações sobre limitações e componentes do Azure Load Balancer, consulte [Azure Load Balancer conceitos e limitações](./concepts-limitations.md)
