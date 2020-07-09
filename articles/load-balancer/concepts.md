---
title: Conceitos de balançador de carga de Azure
description: Visão geral dos conceitos do balançador de carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: cb8b3b58f1029a722121f491d202e245300d1aee
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801017"
---
# <a name="azure-load-balancer-concepts"></a>Conceitos de balançador de carga de Azure

O balanceador de carga fornece várias capacidades tanto para aplicações UDP como para TCP. 

## <a name="load-balancing-algorithm"></a>Algoritmo de balanceamento de carga
Pode criar uma regra de equilíbrio de carga para distribuir o tráfego da parte frontal para uma piscina de backend. O Azure Load Balancer utiliza um algoritmo de hashing para distribuição de fluxos de entrada (não bytes). O equilibrador de carga reescreve os cabeçalhos dos fluxos para ressarcir as instâncias da piscina. Um servidor está disponível para receber novos fluxos quando uma sonda de saúde indica um ponto final saudável.

Por predefinição, o balanceador de carga utiliza um haxixe de cinco tuples.

O haxixe inclui:

- **Endereço IP de origem**
- **Porta de origem**
- **Endereço IP de destino**
- **Porto de destino**
- **Número de protocolo IP para mapear fluxos para servidores disponíveis**

A afinidade com um endereço IP de origem é criada usando um haxixe de dois ou três tuples. Os pacotes do mesmo fluxo chegam no mesmo caso por detrás da extremidade frontal equilibrada de carga. 

A porta de origem muda quando um cliente inicia um novo fluxo a partir da mesma fonte IP. Como resultado, o haxixe de cinco tuples pode fazer com que o tráfego vá para um ponto final diferente.
Para obter mais informações, consulte [configurar o modo de distribuição para O Balancer de Carga Azure](./load-balancer-distribution-mode.md).

A imagem seguinte apresenta a distribuição baseada em hashes:

  ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hashes*

## <a name="application-independence-and-transparency"></a>Independência e transparência da aplicação

O balanceador de carga não interage diretamente com TCP ou UDP ou a camada de aplicação. Qualquer cenário de aplicação de TCP ou UDP pode ser suportado. O balanceador de carga não fecha ou origina fluxos ou interage com a carga útil do fluxo. O balanceador de carga não fornece a funcionalidade de gateway de camada de aplicação. Os apertos de mão do protocolo ocorrem sempre diretamente entre o cliente e a caixa de piscina de fundo. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.

* Cada ponto final é respondido por um VM. Por exemplo, ocorre um aperto de mão TCP entre o cliente e o VM de back-end selecionado. Uma resposta a um pedido para uma frente é uma resposta gerada por um VM de back-end. Quando valida com sucesso a conectividade para uma extremidade frontal, está a validar a conectividade em pelo menos uma máquina virtual de back-end.
* As cargas de aplicação são transparentes para o balançador de carga. Qualquer aplicação UDP ou TCP pode ser suportada.
* Como o equilibrador de carga não interage com a carga útil TCP e fornece descarregamento de TLS, pode construir cenários encriptados abrangentes. A utilização do balançador de carga ganha uma grande escala para aplicações TLS, terminando a ligação TLS na própria VM. Por exemplo, a sua capacidade de teclaragem de sessão TLS é limitada apenas pelo tipo e número de VMs que adiciona à piscina traseira.

## <a name="load-balancer-terminology"></a>Terminologia do balanceador de carga
| Conceito | O que é que isso significa? | Documento detalhado |
| ---------- | ---------- | ----------|
Ligações de saída | Os fluxos da piscina de backend para os IPs públicos são mapeados para o frontend. O Azure traduz ligações de saída para o endereço IP frontal público através da regra de saída de equilíbrio de carga. Esta configuração tem as seguintes vantagens. Fácil upgrade e recuperação de desastres de serviços, porque a frente pode ser mapeada dinamicamente para outra instância do serviço. Gestão de lista de controlo de acessos mais fácil (ACL). Os ACLs expressos como IPs front-end não mudam à medida que os serviços escalam para cima ou para baixo ou são redistribuídos. A tradução das ligações de saída para um número menor de endereços IP do que as máquinas reduz o fardo da implementação de listas de destinatários seguras.| Para saber mais sobre a Tradução de Endereços de Rede De Origem (SNAT) e Equilibrador de Carga Azure, consulte [o Balançador de Carga SNAT e Azure](load-balancer-outbound-connections.md).
Zonas de Disponibilidade | O balanceador de carga padrão suporta capacidades adicionais em regiões onde as Zonas de Disponibilidade estão disponíveis. Estas funcionalidades são incrementais a todos os que o balanceador de carga padrão fornece.  As configurações de Zonas de Disponibilidade estão disponíveis para ambos os tipos de balanceador de carga Standard; público e interno. Um frontend redundante de zona sobrevive à falha da zona utilizando infraestruturas dedicadas em todas as zonas simultaneamente. Além disso, pode garantir um frontend para uma zona específica. Uma frente zonal é servida por infraestruturas dedicadas numa única zona. O equilíbrio de carga cross-zone está disponível para a piscina de backend. Qualquer recurso de máquina virtual numa rede virtual pode fazer parte de uma piscina de backend. O equilibrador de carga básico não suporta zonas.| [Reveja a discussão detalhada das habilidades relacionadas com as Zonas de Disponibilidade](load-balancer-standard-availability-zones.md) e [visão geral das Zonas de Disponibilidade](../availability-zones/az-overview.md) para obter mais informações.
| Portos HA | Pode configurar regras de equilíbrio de carga na porta HA para tornar a sua escala de aplicação e ser altamente fiável. O equilíbrio de carga por fluxo em portos de curta duração do IP do balançador de carga interno é fornecido por estas regras. A funcionalidade é útil quando é impraticável ou indesejável especificar portas individuais. Uma regra das portas HA permite-lhe criar cenários n+1 ativos passivos ou ativos. Estes cenários são para aparelhos virtuais de rede e qualquer aplicação, que requer grandes gamas de portas de entrada. Uma sonda de saúde pode ser usada para determinar quais as extremidades traseiras que devem receber novos fluxos.  Pode utilizar um Grupo de Segurança de Rede para imitar um cenário de alcance portuário. O equilibrador de carga básico não suporta portas HA. | Revisão [detalhada da discussão dos portos de HA](load-balancer-ha-ports-overview.md)
| Vários front-ends | O equilibrador de carga suporta várias regras com vários frontends.  O Balancer de Carga Padrão expande esta capacidade para cenários de saída. As regras de saída são o inverso de uma regra de entrada. A regra de saída cria uma associação para ligações de saída. O balanceador de carga padrão utiliza todos os frontends associados a um recurso de máquina virtual através de uma regra de equilíbrio de carga. Além disso, um parâmetro sobre a regra de equilíbrio de carga permite suprimir uma regra de equilíbrio de carga para fins de conectividade de saída, que permite a seleção de frontends específicos, incluindo nenhum. Para comparação, o balanceador de carga básico seleciona um único frontend aleatoriamente. Não há capacidade de controlar qual o frontend foi selecionado.|


## <a name="limitations"></a><a name = "limitations"></a>Limitações

- Uma regra do balançador de carga não pode abranger duas redes virtuais.  Os frontends e as suas instâncias de backend devem estar localizadas na mesma rede virtual.  

- As funções de trabalhador web sem uma rede virtual e outros serviços da plataforma microsoft podem ser acessíveis a partir de instâncias atrás apenas de um balanceador de carga interno Standard. Não confie nesta acessibilidade, pois o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Se for necessária conectividade de saída quando utilizar um equilibrador de carga interno padrão, a [conectividade de saída](load-balancer-outbound-connections.md) deve ser configurada.

- O balançador de carga fornece o equilíbrio de carga e o encaminhamento da porta para protocolos específicos de TCP ou UDP. As regras de equilíbrio de carga e as regras nat de entrada suportam TCP e UDP, mas não outros protocolos IP, incluindo o ICMP.

- O fluxo de saída de um VM de backend para uma parte frontal de um balanceador de carga interno falhará.

- Reencaminhar fragmentos de IP não é suportado em regras de equilíbrio de carga. A fragmentação ip de pacotes UDP e TCP não é suportada em regras de equilíbrio de carga. As regras de equilíbrio de carga das portas HA podem ser utilizadas para encaminhar os fragmentos de IP existentes. Para mais informações, consulte [a visão geral das portas de alta disponibilidade.](load-balancer-ha-ports-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balanceador de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com a utilização de um Balancer de Carga: criar um, criar VMs com uma extensão IIS personalizada instalada e carregar o equilíbrio da aplicação web entre os VMs.
- Saiba mais [sobre as ligações de saída do Azure Load Balancer](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [as Sondas de Saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre [diagnósticos de balanceadores de carga padrão](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md)
