---
title: Componentes do equilíbrio de carga Azure
description: Visão geral dos componentes do Equilíbrio de Carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 532dc313a673d28ffe4fc66060d6dcb491ce866c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691275"
---
# <a name="azure-load-balancer-components"></a>Componentes do equilíbrio de carga Azure

O Azure Load Balancer contém vários componentes-chave para o seu funcionamento.  

Estes componentes podem ser configurados na sua subscrição através do portal Azure, Azure CLI, Azure PowerShell ou Templates.

## <a name="frontend-ip-configurations"></a>Configurações IP frontend

O endereço IP do equilibrador de carga. É o ponto de contacto dos clientes. Estes endereços podem ser:

- **Endereço IP público**
- **Endereço IP privado**

A seleção do endereço IP determina o **tipo** de equilíbrio de carga criado. A seleção privada de endereços IP cria um equilibrador interno de carga. A seleção de endereços IP públicos cria um equilibrador de carga pública.

## <a name="backend-pool"></a>Conjunto de back-end

O grupo de máquinas virtuais ou instâncias num conjunto de escala de máquina virtual que está a servir o pedido de entrada. Para escalar os custos de forma eficaz para satisfazer os elevados volumes de tráfego que chegam, as diretrizes de computação geralmente recomendam adicionar mais instâncias à piscina de backend. 

O equilibrador de carga reconfigura-se instantaneamente através da reconfiguração automática quando escala as instâncias para cima ou para baixo. A adição ou remoção de VMs da piscina de backend reconfigura o equilibrador de carga sem operações adicionais. O âmbito da piscina de backend é qualquer máquina virtual na rede virtual. 

Ao considerar como projetar o seu backend pool, desenhe para o menor número de recursos individuais de piscina de backend para otimizar a duração das operações de gestão. Não há diferença no desempenho ou escala do avião de dados.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma sonda de saúde é usada para determinar a saúde dos casos na piscina de backend. Pode definir o limiar pouco saudável para as suas sondas de saúde. Quando uma sonda não responde, o equilibrador de carga deixa de enviar novas ligações para os casos pouco saudáveis. Uma falha na sonda não afeta as ligações existentes. A ligação continua até à aplicação:

- Acaba com o fluxo
- O tempo limite de marcha lenta ocorre
- O VM fecha

O Balancer de Carga fornece diferentes tipos de sonda de saúde para pontos finais:

- TCP
- HTTP
- HTTPS

O equilíbrio de carga básico não suporta sondas HTTPS. O equilíbrio de carga básico fecha todas as ligações TCP (incluindo ligações estabelecidas).

## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga

As regras de equilíbrio de carga dizem ao equilibrador de carga o que fazer. Uma regra de equilíbrio de carga mapeia uma configuração IP frontal dada e porta para vários endereços IP e portas de backend.

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

O NAT de entrada rege o tráfego do endereço IP frontend para uma instância de backend dentro da rede virtual. O encaminhamento por porta é feito pela mesma distribuição baseada em hash como o equilíbrio de carga. 

Exemplo de utilização é o Protocolo de Ambiente de Trabalho Remoto (RDP) ou sessões secure Shell (SSH) para separar as instâncias vm dentro de uma rede virtual. Vários pontos finais internos podem ser mapeados para portas no mesmo endereço IP frontal. Os endereços IP front-front-end podem ser usados para administrar remotamente os seus VMs sem uma caixa de salto adicional.

## <a name="outbound-rules"></a>Regras de saída

Uma regra de saída configura a tradução de endereços de rede (NAT) para todas as máquinas virtuais ou instâncias identificadas pela piscina de backend.

O equilibrista básico de carga não suporta as regras de saída.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com o uso de um Balancer de Carga.
- Saiba mais sobre o Equilíbrio de [Carga Azure.](load-balancer-overview.md)
- Saiba mais sobre [o endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Saiba mais sobre [o endereço IP privado](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Saiba mais sobre a utilização de Zonas de [Equilíbrio de Carga Padrão e De Disponibilidade.](load-balancer-standard-availability-zones.md)
- Saiba mais sobre diagnósticos de [balanceadores de carga padrão.](load-balancer-standard-diagnostics.md)
- Saiba mais sobre [o Reset TCP em Idle](load-balancer-tcp-reset.md).
- Saiba mais sobre o [Balancer de Carga Standard com regras](load-balancer-ha-ports-overview.md)de equilíbrio de carga ha ports .
- Aprenda a utilizar o [Balancer de Carga com extremidades múltiplas](load-balancer-multivip-overview.md).
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)
- Saiba mais sobre [os tipos](load-balancer-custom-probe-overview.md#types)de sonda .
- Saiba mais sobre [os limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)do equilíbrio de carga.
- Saiba utilizar [o reencaminhamento do Porto.](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)
- Saiba mais sobre as regras de saída do [balancer de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
