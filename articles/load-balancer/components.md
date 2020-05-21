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
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684978"
---
# <a name="azure-load-balancer-components"></a>Componentes do equilíbrio de carga Azure

O Equilíbrio de Carga Azure é composto por alguns componentes-chave. Estes podem ser configurados na sua subscrição através do portal Azure, Azure CLI, Azure PowerShell ou Templates.

## <a name="frontend-ip-configuration"></a>Configuração IP frontend<a name = "frontend-ip-configurations"></a>

O endereço IP do seu Equilíbrio de Carga Azure. É o ponto de contacto dos clientes. Estes endereços IP podem ser:

- **Endereço IP público**
- **Endereço IP privado**

A natureza do endereço IP determina o **tipo** de equilíbrio de carga criado. A seleção privada de endereços IP cria um equilibrador interno de carga. A seleção de endereços IP públicos cria um equilibrador de carga pública.

|  | Balanceador de Carga Público  | Balanceador de Carga Interno |
| ---------- | ---------- | ---------- |
| Configuração IP frontend| Endereço IP público | Endereço IP privado|
| Descrição | Um equilibrador de carga pública mapeia o IP público e o porto de tráfego de entrada para o IP privado e porto do VM. Os mapas do equilíbrio de carga são contrários para o tráfego de resposta do VM. Pode distribuir tipos específicos de tráfego em vários VMs ou serviços aplicando regras de equilíbrio de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.| Um equilibrador de carga interna distribui tráfego a recursos que estão dentro de uma rede virtual. O Azure restringe o acesso aos endereços IP frontend de uma rede virtual equilibrada. Os endereços IP front-end e as redes virtuais nunca são diretamente expostos a um ponto final da Internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local. |
| SKUs apoiados | Básico, Padrão | Básico, Padrão |

![Exemplo de equilibrador de carga hierárquico](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Conjunto de back-end

O grupo de máquinas virtuais ou instâncias num conjunto de escala de máquina virtual que está a servir o pedido de entrada. Para escalar os custos de forma eficaz para satisfazer os elevados volumes de tráfego que chegam, as diretrizes de computação geralmente recomendam adicionar mais instâncias à piscina de backend.

O equilibrador de carga reconfigura-se instantaneamente através da reconfiguração automática quando escala as instâncias para cima ou para baixo. A adição ou remoção de VMs da piscina de backend reconfigura o equilibrador de carga sem operações adicionais. O âmbito da piscina de backend é qualquer máquina virtual na rede virtual.

Ao considerar como projetar o seu backend pool, desenhe para o menor número de recursos individuais de piscina de backend para otimizar a duração das operações de gestão. Não há diferença no desempenho ou escala do avião de dados.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma sonda de saúde é usada para determinar o estado de saúde dos casos na piscina de backend. Ao criar um Balancer de Carga, deve configurar uma sonda de saúde que o seu Balancer de Carga pode usar para determinar se uma instância é saudável e encaminhar o tráfego para o mesmo.

Pode definir o limiar pouco saudável para as suas sondas de saúde. Quando uma sonda não responde, o Balancer load para de enviar novas ligações para os casos pouco saudáveis. Uma falha na sonda não afeta as ligações existentes. A ligação continua até à aplicação:

- Acaba com o fluxo
- O tempo limite de marcha lenta ocorre
- O VM fecha

O Load Balancer fornece diferentes tipos de sonda de saúde para pontos finais: TCP, HTTP e HTTPS.

O Equilíbrio de Carga Básico não suporta sondas HTTPS. O Equilíbrio de Carga Básico fecha todas as ligações TCP (incluindo ligações estabelecidas).

## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga

Uma regra do Balancer de Carga é usada para definir como o tráfego de entrada é distribuído para **todas** as instâncias dentro do Backend Pool. Uma regra de equilíbrio de carga mapeia uma configuração IP frontend dada e porta para vários endereços IP e portas backend.

Por exemplo, se quiser que o tráfego no porto 80 (ou outra porta) do seu IP frontal seja encaminhado para a porta 80 de todas as instâncias de backend, usaria uma regra de Equilíbrio de Carga para o conseguir.

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

Uma regra de entrada na NAT reencaminha o tráfego de entrada enviado para um endereço IP frontend selecionado e combinação de porta para uma máquina virtual **específica** ou instância na piscina de backend. O encaminhamento por porta é feito pela mesma distribuição baseada em hash como o equilíbrio de carga.

Por exemplo, se quiser sessões de Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH) para separar as instâncias vm numa piscina de backend. Vários pontos finais internos podem ser mapeados para portas no mesmo endereço IP Frontend. Os endereços IP Frontend podem ser usados para administrar remotamente os seus VMs sem uma caixa de salto adicional.

## <a name="outbound-rules"></a>Regras de saída

Uma regra de saída configura a tradução de endereços de rede (NAT) para todas as máquinas virtuais ou instâncias identificadas pela piscina de backend. Isto permite que os casos no backend comuniquem (saída) à internet ou a outros pontos finais.

O equilibrista básico de carga não suporta as regras de saída.

## <a name="next-steps"></a>Próximos passos

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com o uso de um Balancer de Carga.
- Saiba mais sobre o Equilíbrio de [Carga Azure.](load-balancer-overview.md)
- Saiba mais sobre [o endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Saiba mais sobre [o endereço IP privado](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Saiba mais sobre a utilização de Zonas de [Equilíbrio de Carga Padrão e De Disponibilidade.](load-balancer-standard-availability-zones.md)
- Saiba mais sobre diagnósticos de [balanceadores de carga padrão.](load-balancer-standard-diagnostics.md)
- Saiba mais sobre [o Reset TCP em Idle](load-balancer-tcp-reset.md).
- Saiba mais sobre o [Balancer de Carga Standard com regras](load-balancer-ha-ports-overview.md)de equilíbrio de carga ha ports .
- Saiba utilizar o [Balancer de Carga com configurações IP de fachada múltiplas](load-balancer-multivip-overview.md).
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)
- Saiba mais sobre [os tipos](load-balancer-custom-probe-overview.md#types)de sonda .
- Saiba mais sobre [os limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)do equilíbrio de carga.
- Saiba utilizar [o reencaminhamento do Porto.](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)
- Saiba mais sobre as regras de saída do [balancer de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
