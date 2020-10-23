---
title: Conceitos de balançador de carga de Azure
description: Visão geral dos conceitos do balançador de carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 28c3e4ade2c78a1a77ef7263463803e40727ba8f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315309"
---
# <a name="azure-load-balancer-algorithm"></a>Algoritmo do balançador de carga Azure

O balanceador de carga fornece várias capacidades tanto para aplicações UDP como para TCP.

## <a name="load-balancing-algorithm"></a>Algoritmo de balanceamento de carga

Pode criar uma regra de equilíbrio de carga para distribuir o tráfego da parte frontal para uma piscina de backend. O Azure Load Balancer utiliza um algoritmo de hashing para distribuição de fluxos de entrada (não bytes). O equilibrador de carga reescreve os cabeçalhos dos fluxos para ressarcir as instâncias da piscina. Um servidor está disponível para receber novos fluxos quando uma sonda de saúde indica um ponto final saudável.

Por predefinição, o balanceador de carga utiliza um haxixe de cinco tuples.

O haxixe inclui:

- **Endereço IP de origem**
- **Porta de origem**
- **Endereço IP de destino**
- **Porta de destino**
- **Número de protocolo IP para mapear fluxos para servidores disponíveis**

A afinidade com um endereço IP de origem é criada usando um haxixe de dois ou três tuples. Os pacotes do mesmo fluxo chegam no mesmo caso por detrás da extremidade frontal equilibrada de carga.

A porta de origem muda quando um cliente inicia um novo fluxo a partir da mesma fonte IP. Como resultado, o haxixe de cinco tuples pode fazer com que o tráfego vá para um ponto final diferente.
Para obter mais informações, consulte [configurar o modo de distribuição para O Balancer de Carga Azure](./load-balancer-distribution-mode.md).

A imagem seguinte apresenta a distribuição baseada em hashes:

![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

*Figura: distribuição baseada em hashes*

## <a name="application-independence-and-transparency"></a>Independência e transparência da aplicação

O balanceador de carga não interage diretamente com TCP ou UDP ou a camada de aplicação. Qualquer cenário de aplicação de TCP ou UDP pode ser suportado. O balanceador de carga não fecha ou origina fluxos ou interage com a carga útil do fluxo. O balanceador de carga não fornece a funcionalidade de gateway de camada de aplicação. Os apertos de mão do protocolo ocorrem sempre diretamente entre o cliente e a caixa de piscina de fundo. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.

- Cada ponto final é respondido por um VM. Por exemplo, ocorre um aperto de mão TCP entre o cliente e o VM de back-end selecionado. Uma resposta a um pedido para uma frente é uma resposta gerada por um VM de back-end. Quando valida com sucesso a conectividade para uma extremidade frontal, está a validar a conectividade em pelo menos uma máquina virtual de back-end.
- As cargas de aplicação são transparentes para o balançador de carga. Qualquer aplicação UDP ou TCP pode ser suportada.
- Como o equilibrador de carga não interage com a carga útil TCP e fornece descarregamento de TLS, pode construir cenários encriptados abrangentes. A utilização do balançador de carga ganha uma grande escala para aplicações TLS, terminando a ligação TLS na própria VM. Por exemplo, a sua capacidade de teclaragem de sessão TLS é limitada apenas pelo tipo e número de VMs que adiciona à piscina traseira.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [componentes](components.md) que compõem o Azure Load Balancer.
- Consulte [Criar um Balanceador de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com a utilização de um Balancer de Carga: criar um, criar VMs com uma extensão IIS personalizada instalada e carregar o equilíbrio da aplicação web entre os VMs.
- Saiba mais [sobre as ligações de saída do Azure Load Balancer](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [as Sondas de Saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre [diagnósticos de balanceadores de carga padrão](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md)
