---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como funciona o Balancer de Carga e como usá-lo na nuvem.
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
ms.openlocfilehash: 2853b1567618127866a4e9c61d81e599d3100823
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064270"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

*O equilíbrio de carga* refere-se à distribuição uniforme da carga (tráfego de rede de entrada) através de um grupo de recursos ou servidores de backend. 

O Azure Load Balancer funciona na camada quatro do modelo de Interconexão de Sistemas Abertos (OSI). É o único ponto de contacto para os clientes. O Balancer load distribui fluxos de entrada que chegam à extremidade dianteira do equilibrador de carga para as instâncias da piscina de backend. Estes fluxos são de acordo com regras configuradas de equilíbrio de carga e sondas de saúde. As instâncias de piscina de backend podem ser Máquinas Virtuais Azure ou instâncias em um conjunto de escala de máquina virtual.

Um **[equilibrador](./concepts-limitations.md#publicloadbalancer)** de carga público pode fornecer ligações de saída para máquinas virtuais (VMs) dentro da sua rede virtual. Estas ligações são realizadas traduzindo os seus endereços IP privados para endereços IP públicos. Os Balanceadores de Carga Pública são usados para carregar o tráfego de internet de equilíbrio para os seus VMs.

Um **[equilibrista de carga interno (ou privado)](./concepts-limitations.md#internalloadbalancer)** é utilizado onde os IPs privados são necessários apenas na extremidade frontal. Os equilibradores internos de carga são usados para carregar o tráfego de equilíbrio dentro de uma rede virtual. Um frontend de equilíbrio de carga pode ser acedido a partir de uma rede no local em um cenário híbrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Figura: Equilibrar aplicações a vários níveis utilizando equilibrantes de carga públicos e internos*

Para obter mais informações sobre os componentes individuais do equilíbrio de carga, consulte componentes e limitações do [Equilíbrio de Carga Azure](./concepts-limitations.md)

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se precisa de alto desempenho, baixa latência, equilíbrio de carga camada 7, veja o que é o Portal de [Aplicação Azure?](../application-gateway/overview.md) Se procura o equilíbrio global de carga slédi, veja o que é o Gestor de [Tráfego?](../traffic-manager/traffic-manager-overview.md) Os seus cenários de ponta a ponta podem beneficiar da combinação destas soluções.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-load-balancer"></a>Porquê usar o Equilíbrio de Carga Azure?
Com o Standard Load Balancer, pode escalar as suas aplicações e criar serviços altamente disponíveis. O equilibrista de carga suporta cenários de entrada e saída. O equilibrador de carga proporciona baixa latência e alta afluência, e escala até milhões de fluxos para todas as aplicações TCP e UDP.

Os cenários-chave que pode realizar usando o Standard Load Balancer incluem:

- Balanço de carga **[tráfego interno](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** e **[externo](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** para máquinas virtuais Azure.

- Aumentar a disponibilidade distribuindo recursos **[dentro](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** e **[fora](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** de zonas.

- Configure **[a conectividade de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** para máquinas virtuais Azure.

- Utilize sondas de **[saúde](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** para monitorizar recursos equilibrados em carga.

- Utilize **[o encaminhamento](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** do porto para aceder a máquinas virtuais numa rede virtual através de endereço IP público e porta.

- Ativar o suporte para **[o equilíbrio de carga](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** do **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- O Standard Load Balancer fornece métricas multidimensionais através do [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview).  Estas métricas podem ser filtradas, agrunadas e partidas para uma determinada dimensão.  Fornecem insights atuais e históricos sobre o desempenho e a saúde do seu serviço.  A Saúde dos Recursos também é apoiada. Reveja os **[diagnósticos de balanceadores de carga padrão](load-balancer-standard-diagnostics.md)** para mais detalhes.

- Serviços de balanço de carga em **[várias portas, vários endereços IP, ou ambos.](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**

- Mover recursos de equilíbrio de carga **[internos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** e **[externos](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** em todas as regiões de Azure.

- Balanço de carga Fluxo de TCP e UDP em todas as portas simultaneamente utilizando **[portas HA](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Seguro por padrão

O Standard Load Balancer baseia-se no modelo de segurança da rede zero trust no seu núcleo. O Balancer de Carga Standard é seguro por defeito e faz parte da sua rede virtual. A rede virtual é uma rede privada e isolada.  Isto significa que os equilibradores de carga padrão e os endereços IP públicos padrão estão fechados a fluxos de entrada, a menos que sejam abertos por Grupos de Segurança da Rede. Os NSGs são usados para permitir explicitamente o tráfego permitido.  Se não tiver um NSG numa subnet ou NIC do seu recurso virtual de máquina, o tráfego não está autorizado a chegar a este recurso. Para saber mais sobre nsgs e como aplicá-los para o seu cenário, consulte [Network Security Groups](../virtual-network/security-overview.md).
O Equilíbrio de Carga Básico está aberto à internet por padrão.


## <a name="pricing-and-sla"></a>Preços e SLA

Para obter informações sobre os preços do Equilíbrio de Carga Padrão, consulte [os preços do Balancer de Carga](https://azure.microsoft.com/pricing/details/load-balancer/).
O Balanceador de Carga Básico é disponibilizado sem custos.
Consulte [o SLA para o Balancer de Carga](https://aka.ms/lbsla). O Equilíbrio básico de carga não tem SLA.

## <a name="next-steps"></a>Passos seguintes

Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com o uso de um Balancer de Carga.

Para obter mais informações sobre limitações e componentes do Equilíbrio de Carga Azure, consulte [conceitos e limitações do Azure Load Balancer](./concepts-limitations.md)
