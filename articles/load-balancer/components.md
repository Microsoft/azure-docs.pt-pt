---
title: Componentes do balançador de carga Azure
description: Visão geral dos componentes do balançador de carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: b696cdf2d54c42d3967041c5d10b1bd9bb5a3065
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84448687"
---
# <a name="azure-load-balancer-components"></a>Componentes do balançador de carga Azure

O Azure Load Balancer é composto por alguns componentes-chave. Estes podem ser configurados na sua subscrição através do portal Azure, Azure CLI, Azure PowerShell ou Modelos.

## <a name="frontend-ip-configuration"></a>Configuração IP frontend<a name = "frontend-ip-configurations"></a>

O endereço IP do seu Balançador de Carga Azure. É o ponto de contacto dos clientes. Estes endereços IP podem ser:

- **Endereço IP público**
- **Endereço IP privado**

A natureza do endereço IP determina o **tipo** de balançador de carga criado. A seleção de endereços IP privados cria um equilibrador de carga interno. A seleção de endereços IP públicos cria um equilibrador de carga pública.

|  | Balanceador de Carga Público  | Balanceador de Carga Interno |
| ---------- | ---------- | ---------- |
| Configuração IP frontend| Endereço IP público | Endereço IP privado|
| Descrição | Um equilibrador de carga pública mapeia o IP público e o porto de tráfego de entrada para o IP privado e porto do VM. O equilibrador de carga mapeia o tráfego ao contrário para o tráfego de resposta do VM. Pode distribuir tipos específicos de tráfego através de vários VMs ou serviços aplicando regras de equilíbrio de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.| Um equilibrador de carga interno distribui tráfego por recursos que estão dentro de uma rede virtual. O Azure restringe o acesso aos endereços IP frontend de uma rede virtual que é equilibrada em carga. Os endereços IP front-end e as redes virtuais nunca são diretamente expostos a um ponto final da Internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local. |
| SKUs apoiado | Básico, Padrão | Básico, Padrão |

![Exemplo do balançador de carga hierarquizador de carga](./media/load-balancer-overview/load-balancer.png)

O Balancer de Carga pode ter múltiplos IPs frontend. Saiba mais sobre [vários frontends](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Conjunto de back-end

O grupo de máquinas virtuais ou instâncias em um conjunto de escala de máquina virtual que está a servir o pedido de entrada. Para escalar os custos de forma eficaz para satisfazer volumes elevados de tráfego de entrada, as diretrizes de computação geralmente recomendam adicionar mais instâncias ao pool backend.

O balançador de carga reconfigura-se instantaneamente através da reconfiguração automática quando escala as instâncias para cima ou para baixo. Adicionar ou remover VMs do pool de backend reconfigura o balançador de carga sem operações adicionais. O âmbito da piscina de backend é qualquer máquina virtual na rede virtual.

Ao considerar como desenhar o seu pool backend, desenhe para o menor número de recursos individuais de backend pool para otimizar a duração das operações de gestão. Não há diferença no desempenho ou escala do plano de dados.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma sonda de saúde é usada para determinar o estado de saúde dos casos na piscina de backend. Ao criar um Balancer de Carga, deve configurar uma sonda de saúde que o seu Balancer de Carga pode usar para determinar se um caso é saudável e encaminhar o tráfego para ele.

Pode definir o limiar insalubre para as suas sondas de saúde. Quando uma sonda não responde, o Balancer de Carga deixa de enviar novas ligações para as situações pouco saudáveis. Uma falha na sonda não afeta as ligações existentes. A ligação continua até à aplicação:

- Termina o fluxo
- O tempo limite ocorrem
- O VM desliga

O Load Balancer fornece diferentes tipos de sondas de saúde para pontos finais: TCP, HTTP e HTTPS. [Saiba mais sobre as sondas Load Balancer Health](load-balancer-custom-probe-overview.md).

O Balancer de Carga Básica não suporta sondas HTTPS. O Balanceador de Carga Básica fecha todas as ligações TCP (incluindo ligações estabelecidas).

## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga

Uma regra do Balancer de Carga é utilizada para definir como o tráfego de entrada é distribuído em **todas as** instâncias dentro do Pool Backend. Uma regra de equilíbrio de carga mapeia uma determinada configuração IP frontend e porta para vários endereços IP backend e portas.

Por exemplo, se você gostaria que o tráfego na porta 80 (ou outra porta) do seu IP frontend fosse encaminhado para a porta 80 de todas as suas instâncias de backend, você usaria uma regra de equilíbrio de carga para o conseguir.

### <a name="high-availability-ports"></a>Portos de Alta Disponibilidade

Uma regra do Balanceador de Carga configurada com "protocolo - tudo e porta - 0". Isto permite fornecer uma única regra para equilibrar todos os fluxos TCP e UDP que chegam a todas as portas de um Balanceador de Carga Padrão interno. A decisão de equilibrar a carga é tomada por fluxo. Esta ação baseia-se na seguinte ligação de cinco tuples: 
1. 
    endereço IP de origem
  
2. porta de origem
3. endereço IP de destino
4. porta de destino
5. protocolo

As regras de equilíbrio de carga das portas HA ajudam-no com cenários críticos, tais como alta disponibilidade e escala para aparelhos virtuais de rede (NVAs) dentro de redes virtuais. A funcionalidade também pode ajudar quando um grande número de portas deve ser equilibrada em carga.

Você pode saber mais sobre [portas HA.](load-balancer-ha-ports-overview.md)

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

Uma regra NAT de entrada encaminha o tráfego de entrada enviado para um endereço IP frontend selecionado e combinação de porta para uma máquina virtual **específica** ou instância na piscina de backend. O encaminhamento portuário é feito pela mesma distribuição baseada em haxixe que o equilíbrio de carga.

Por exemplo, se quiser sessões de Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH) para separar as instâncias VM num pool de backend. Vários pontos finais internos podem ser mapeados para portas no mesmo endereço IP frontend. Os endereços IP frontend podem ser utilizados para administrar remotamente os seus VMs sem uma caixa de salto adicional.

As regras NAT de entrada no contexto dos conjuntos de escala de máquina virtual (VMSS) são piscinas NAT de entrada. Saiba mais sobre [os componentes do Balanceador de Carga e VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Regras de saída

Uma regra de saída configura a tradução de endereço de rede de saída (NAT) para todas as máquinas virtuais ou instâncias identificadas pelo pool de backend. Isto permite que as instâncias no backend se comuniquem (saída) para a internet ou outros pontos finais.

Saiba mais sobre [ligações e regras de saída.](load-balancer-outbound-connections.md)

O equilibrador de carga básico não suporta as regras de saída.

## <a name="next-steps"></a>Próximos passos

- Consulte [Criar um balanceador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com um balanceador de carga.
- Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [o Endereço IP Público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Saiba mais sobre [o Endereço IP Privado](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Saiba como utilizar [o Balancer de Carga Padrão e as Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [diagnósticos de balanceadores de carga padrão](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [o Reset TCP em Idle](load-balancer-tcp-reset.md).
- Saiba mais sobre [o Balanceador de Carga Padrão com as regras de equilíbrio de carga dos portos HA](load-balancer-ha-ports-overview.md).
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md)
- Saiba mais sobre [os limites do balançador de carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Saiba como utilizar [o encaminhamento do Porto.](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)
