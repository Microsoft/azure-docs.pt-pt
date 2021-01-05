---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como funciona o Balanceador de Carga e como usá-lo na nuvem.
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
ms.openlocfilehash: e8d0afab14d02ffe16db535f173456b55ab53d69
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860939"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

*O equilíbrio de carga* refere-se à distribuição uniforme da carga (tráfego de rede de entrada) através de um grupo de recursos ou servidores de backend. 

O Azure Load Balancer opera na quarta camada do modelo Open Systems Interconnection (OSI). É o único ponto de contacto para os clientes. O Balancer de Carga distribui fluxos de entrada que chegam à parte frontal do balançador de carga para anular as instâncias da piscina. Estes fluxos são de acordo com regras de equilíbrio de carga configuradas e sondas de saúde. As instâncias de backend pool podem ser Máquinas Virtuais Azure ou instâncias em um conjunto de escala de máquina virtual.

Um **[equilibrador de carga pública](./components.md#frontend-ip-configurations)** pode fornecer ligações de saída para máquinas virtuais (VMs) dentro da sua rede virtual. Estas ligações são realizadas traduzindo os seus endereços IP privados para endereços IP públicos. Os Balançadores de Carga Pública são utilizados para carregar o tráfego de internet de equilíbrio para os seus VMs.

Um **[equilibrador de carga interno (ou privado)](./components.md#frontend-ip-configurations)** é utilizado onde os IPs privados são necessários apenas no frontend. Os balançadores de carga internos são utilizados para carregar o tráfego de equilíbrio dentro de uma rede virtual. Um frontend de balançador de carga pode ser acedido a partir de uma rede no local num cenário híbrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Balanceador de Carga do Azure">
</p>

*Figura: Equilibrar aplicações multi-camadas utilizando o balanceador de carga público e interno*

Para obter mais informações sobre os componentes do balançador de carga individual, consulte [os componentes do Balançador de Carga Azure](./components.md).

## <a name="why-use-azure-load-balancer"></a>Porquê usar o Equilibrador de Carga Azure?
Com o Balancer de Carga Padrão, pode escalar as suas aplicações e criar serviços altamente disponíveis. O equilibrador de carga suporta cenários de entrada e saída. O balanceador de carga proporciona baixa latência e alta produção, e escala até milhões de fluxos para todas as aplicações TCP e UDP.

Os cenários-chave que pode realizar usando o Balancer de Carga Padrão incluem:

- Balance o tráfego **[interno](./quickstart-load-balancer-standard-internal-portal.md)** e **[externo](./tutorial-load-balancer-standard-manage-portal.md)** para as máquinas virtuais Azure.

- Aumentar a disponibilidade distribuindo recursos **[dentro](./tutorial-load-balancer-standard-public-zonal-portal.md)** e **[em todas as](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** zonas.

- Configure **[a conectividade de saída](./load-balancer-outbound-connections.md)** para máquinas virtuais Azure.

- Utilize **[sondas sanitárias](./load-balancer-custom-probe-overview.md)** para monitorizar recursos equilibrados em carga.

- Utilize **[o encaminhamento de portas](./tutorial-load-balancer-port-forwarding-portal.md)** para aceder a máquinas virtuais numa rede virtual por endereço IP público e porta.

- Ativar o suporte para **[o equilíbrio](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** de carga do **[IPv6](../virtual-network/ipv6-overview.md)**.

- O Balanceador de Carga Padrão fornece métricas multidimensionais através do [Azure Monitor.](../azure-monitor/overview.md)  Estas métricas podem ser filtradas, agrupadas e partidas para uma determinada dimensão.  Fornecem insights atuais e históricos sobre o desempenho e saúde do seu serviço.  A Saúde dos Recursos também é apoiada. Reveja **[o Diagnóstico Padrão do Balanceador de Carga](load-balancer-standard-diagnostics.md)** para mais detalhes.

- Carregue os serviços de balanço em **[várias portas, vários endereços IP, ou ambos](./load-balancer-multivip-overview.md)**.

- Mover recursos de equilibradores de carga **[internos](./move-across-regions-internal-load-balancer-portal.md)** e **[externos](./move-across-regions-external-load-balancer-portal.md)** em todas as regiões de Azure.

- Equilíbrio de carga TCP e UDP fluem em todas as portas simultaneamente utilizando **[portas HA](./load-balancer-ha-ports-overview.md)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Proteger por defeito

O Balancer de Carga Padrão é construído no modelo de segurança da rede de confiança zero no seu núcleo. O Balancer de Carga Padrão é seguro por predefinição e parte da sua rede virtual. A rede virtual é uma rede privada e isolada.  Isto significa que os balanceadores de carga padrão e os endereços IP públicos padrão estão fechados para fluxos de entrada, a menos que sejam abertos por Grupos de Segurança de Rede. Os NSGs são usados para permitir explicitamente o tráfego permitido.  Se não tiver um NSG numa sub-rede ou NIC do seu recurso de máquina virtual, o tráfego não pode chegar a este recurso. Para saber mais sobre os NSGs e como aplicá-los para o seu cenário, consulte [os Grupos de Segurança da Rede.](../virtual-network/network-security-groups-overview.md)
O Balancer de Carga Básica está aberto à internet por padrão. Além disso, o Balancer de Carga não armazena os dados do cliente.

## <a name="pricing-and-sla"></a>Preços e SLA

Para obter informações sobre preços do balanceador de carga padrão, consulte [os preços do balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer/).
O Balanceador de Carga Básico é disponibilizado sem custos.
Ver [SLA para balanceador de carga](https://aka.ms/lbsla). O Balanceador de Carga Básica não tem SLA.

## <a name="whats-new"></a>Novidades

Subscreva o feed RSS e veja as atualizações mais recentes da funcionalidade Azure Load Balancer na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)

## <a name="next-steps"></a>Passos seguintes

Consulte [Criar um equilibrador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com um equilibrador de carga.

Para obter mais informações sobre as limitações e componentes do Balançador de Carga Azure consulte os componentes do [Balançador de Carga Azure](./components.md) e [os conceitos do Balançador de Carga Azure](./concepts.md)
