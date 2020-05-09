---
title: Conceitos de Balancer de Carga Azure
description: Visão geral dos conceitos de Equilíbrio de Carga Azure
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
ms.openlocfilehash: 2e2e11e1dc16fd99a82bc2fbdc7b72399bc6ae97
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871922"
---
# <a name="azure-load-balancer-concepts"></a>Conceitos de Balancer de Carga Azure

O balancer de carga fornece várias capacidades tanto para aplicações UDP como TCP. 

## <a name="load-balancing-algorithm"></a>Algoritmo de balanceamento de carga
Você pode criar uma regra de equilíbrio de carga para distribuir o tráfego da extremidade frontal para uma piscina de backend. O Azure Load Balancer utiliza um algoritmo de hashing para distribuição de fluxos de entrada (não bytes). O equilibrador de carga reescreve os cabeçalhos dos fluxos para apoiar as instâncias da piscina. Um servidor está disponível para receber novos fluxos quando uma sonda de saúde indica um ponto final saudável.

Por padrão, o equilibrador de carga utiliza um hash de cinco tuple.

O hash inclui:

- **Endereço IP de origem**
- **Porta-fonte**
- **Endereço IP de destino**
- **Porto de destino**
- **Número de protocolo IP para mapas de fluxos para servidores disponíveis**

A afinidade com um endereço IP de origem é criada utilizando um hash de dois ou três tuple. Os pacotes do mesmo fluxo chegam na mesma instância atrás da extremidade frontal equilibrada. 

A porta de origem muda quando um cliente inicia um novo fluxo a partir da mesma fonte IP. Como resultado, o hash de cinco tuple pode fazer com que o tráfego vá para um ponto final diferente.
Para mais informações, consulte Configure o modo de [distribuição para O Equilíbrio de Carga Azure](./load-balancer-distribution-mode.md).

A imagem seguinte apresenta a distribuição baseada em hashes:

  ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hashes*

## <a name="application-independence-and-transparency"></a>Independência e transparência da aplicação

O equilibrador de carga não interage diretamente com TCP ou UDP ou com a camada de aplicação. Qualquer cenário de aplicação TCP ou UDP pode ser suportado. O equilibrista de carga não fecha nem origina fluxos nem interage com a carga útil do fluxo. O equilibrador de carga não fornece a funcionalidade de gateway da camada de aplicação. Os apertos de mão protocolares ocorrem sempre diretamente entre o cliente e a instância de piscina de fundo. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.

* Cada ponto final é respondido por um VM. Por exemplo, ocorre um aperto de mão TCP entre o cliente e o VM traseiro selecionado. Uma resposta a um pedido para uma frente é uma resposta gerada por um VM de back-end. Quando valida com sucesso a conectividade para uma extremidade frontal, está a validar a conectividade em toda a sua ligação até pelo menos uma máquina virtual de back-end.
* As cargas de aplicação são transparentes para o equilibrador de carga. Qualquer aplicação UDP ou TCP pode ser suportada.
* Como o equilibrista de carga não interage com a carga útil do TCP e fornece a descarga de TLS, pode construir cenários encriptados abrangentes. A utilização do equilibrador de carga ganha uma grande escala para aplicações TLS, terminando a ligação TLS no próprio VM. Por exemplo, a sua capacidade de chave de sessão TLS é limitada apenas pelo tipo e número de VMs que adiciona à piscina de back-end.

## <a name="load-balancer-terminology"></a>Terminologia do Equilíbrio de Carga
| Conceito | O que é que isso significa? | Documento detalhado |
| ---------- | ---------- | ----------|
Ligações de saída | Os fluxos da piscina de backend para os IPs públicos estão mapeados para a frente. O Azure traduz ligações de saída para o endereço IP frontal público através da regra de saída de equilíbrio de carga. Esta configuração tem as seguintes vantagens. Fácil atualização e recuperação de desastres de serviços, porque a parte frontal pode ser mapeada dinamicamente para outra instância do serviço. Gestão da lista de controlo de acesso (ACL) mais fácil. Os ACLs expressos como IPs frontais não mudam à medida que os serviços escalam para cima ou para baixo ou são redistribuídos. Traduzir as ligações de saída a um número menor de endereços IP do que as máquinas reduz o fardo da implementação de listas de destinatários seguros.| Para saber mais sobre a Tradução de Endereços da Rede Source (SNAT) e o Equilíbrio de Carga Azure, consulte [SNAT e Azure Load Balancer](load-balancer-outbound-connections.md#snat).
Zonas de Disponibilidade | O equilibrador de carga padrão suporta capacidades adicionais em regiões onde existem Zonas de Disponibilidade. Estas funcionalidades são incrementais a todos os balanceadores de carga padrão proporcionados.  Configurações de Zonas de Disponibilidade estão disponíveis para ambos os tipos de equilibrantes de carga Standard; público e interno. Uma extremidade frontal redundante sobrevive à falha da zona utilizando infraestruturas dedicadas em todas as zonas simultaneamente. Além disso, pode garantir uma frontend a uma zona específica. Um frontend zonal é servido por infraestruturas dedicadas numa única zona. O equilíbrio de carga transversal está disponível para a piscina de backend. Qualquer recurso virtual de máquina numa rede virtual pode fazer parte de uma piscina de backend. O equilibrador básico de carga não suporta zonas.| Reveja [a discussão detalhada sobre as capacidades relacionadas](load-balancer-standard-availability-zones.md) com as Zonas de Disponibilidade e a visão geral das [Zonas de Disponibilidade](../availability-zones/az-overview.md) para obter mais informações.
| Portos HA | Pode configurar regras de equilíbrio de carga da porta HA para tornar a sua escala de aplicação e ser altamente fiável. O equilíbrio de carga por caudal em portas de curta duração do IP frontal do equilibrista interno é fornecido por estas regras. A funcionalidade é útil quando é impraticável ou indesejável especificar portas individuais. Uma regra de portas HA permite criar cenários n+1 ativos ou ativos ativos ativos. Estes cenários são para aparelhos virtuais de rede e qualquer aplicação, que requer grandes gamas de portas de entrada. Uma sonda de saúde pode ser usada para determinar quais as extremidades traseiras que devem receber novos fluxos.  Pode utilizar um Grupo de Segurança de Rede para imitar um cenário de alcance portuário. O equilibrista básico de carga não suporta portas HA. | Revisão [detalhada da discussão sobre os Portos ha](load-balancer-ha-ports-overview.md)
| Vários front-ends | O equilibrador de carga suporta várias regras com várias extremidades dianteiras.  O Standard Load Balancer expande esta capacidade para cenários de saída. As regras de saída são o inverso de uma regra de entrada. A regra de saída cria uma associação para ligações de saída. O equilíbrio de carga padrão utiliza todas as extremidades dianteiras associadas a um recurso de máquina virtual através de uma regra de equilíbrio de carga. Além disso, um parâmetro na regra de equilíbrio de carga permite-lhe suprimir uma regra de equilíbrio de carga para efeitos de conectividade de saída, o que permite a seleção de frontendas específicas, incluindo nenhuma. Para comparação, o equilíbrio de carga basic seleciona uma única extremidade frontal aleatoriamente. Não há uma capacidade de controlar qual frontend foi selecionado.|


## <a name="limitations"></a><a name = "limitations"></a>Limitações

- Uma regra de equilíbrio de carga não pode abranger duas redes virtuais.  As extremidades dianteiras e as suas instâncias de backend devem estar localizadas na mesma rede virtual.  

- As Funções dos Trabalhadores web sem uma rede virtual e outros serviços de plataforma da Microsoft podem ser acessíveis a partir de instâncias por trás apenas de um equilibrador de carga interna Standard. Não confie nesta acessibilidade, pois o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Se for necessária conectividade de saída quando utilizar um equilíbrio interno padrão de carga, a conectividade de [saída](load-balancer-outbound-connections.md) deve ser configurada.

- O equilibrador de carga fornece o equilíbrio de carga e o encaminhamento da porta para protocolos específicos de TCP ou UDP. As regras de equilíbrio de carga e as regras de entrada na NAT apoiam a TCP e a UDP, mas não outros protocolos IP, incluindo o ICMP.

- O fluxo de saída de um VM traseiro para uma extremidade frontal de um Balancer de carga interna falhará.

- O reencaminhamento de fragmentos de IP não é suportado em regras de equilíbrio de carga. A fragmentação ip dos pacotes UDP e TCP não é suportada em regras de equilíbrio de carga. As regras de equilíbrio de carga das portas HA podem ser utilizadas para encaminhar os fragmentos ip existentes. Para mais informações, consulte a visão geral das portas de [alta disponibilidade.](load-balancer-ha-ports-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com a utilização de um Balancer de Carga: criar um, criar VMs com uma extensão IIS personalizada instalada e carregar o equilíbrio da aplicação web entre os VMs.
- Saiba mais sobre as ligações de saída do [Azure Load Balancer](load-balancer-outbound-connections.md).
- Saiba mais sobre o Equilíbrio de [Carga Azure.](load-balancer-overview.md)
- Saiba mais sobre sondas de [saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre diagnósticos de [balanceadores de carga padrão.](load-balancer-standard-diagnostics.md)
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)
