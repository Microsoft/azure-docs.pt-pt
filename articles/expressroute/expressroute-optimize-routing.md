---
title: 'Otimizar o encaminhamento - circuitos do ExpressRoute: Azure | Microsoft Docs'
description: Esta página disponibiliza detalhes sobre como otimizar o encaminhamento quando tem mais do que um circuito do ExpressRoute que se ligam entre a rede da Microsoft e a sua rede empresarial.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883572"
---
# <a name="optimize-expressroute-routing"></a>Otimizar o Encaminhamento do ExpressRoute
Se tem vários circuitos do ExpressRoute, significa que tem mais do que um caminho para se ligar à Microsoft. Sendo assim, o encaminhamento poderá não ser o ideal, ou seja, o tráfego poderá optar por um caminho mais longo para chegar à Microsoft e da Microsoft à sua rede. Quanto mais longo for o caminho de rede, maior será a latência. A latência tem um impacto direto no desempenho das aplicações e na experiência do utilizador. Este artigo ilustra este problema e explica como otimizar o encaminhamento com as tecnologias de encaminhamento padrão.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Encaminhamento inferior ao ideal do cliente para a Microsoft
Vamos analisar o problema de encaminhamento com um exemplo. Imagine que tem dois escritórios nos EUA, um em Los Angeles e outro em Nova Iorque. Os escritórios estão ligados através de uma Rede Alargada (WAN), que pode ser a sua própria rede principal ou a VPN de IP do seu fornecedor de serviços. Tem dois circuitos do ExpressRoute, um nos EUA Oeste e o outro nos EUA Leste, que também estão ligados através da WAN. Tem, obviamente, dois caminhos para se ligar à rede da Microsoft. Imagine agora que tem a implementação do Azure (por exemplo, o Serviço de Aplicações do Azure) nos E.U.A. Oeste e nos E.U.A. Leste. A sua intenção é ligar os utilizadores em Los Angeles ao Azure dos EUA Oeste e os utilizadores em Nova Iorque ao Azure dos EUA Leste, porque o administrador de serviços anuncia que os utilizadores em cada escritório acederão aos serviços mais próximos do Azure, para uma experiência ideal. Infelizmente, o plano funciona bem para os utilizadores da costa leste mas não para os utilizadores da costa oeste. A causa do problema é a seguinte. Em cada circuito do ExpressRoute, anunciamos o prefixo no Azure dos EUA Leste (23.100.0.0/16) e o prefixo no Azure dos EUA Oeste (13.100.0.0/16). Se não souber que prefixo corresponde a que região, não conseguirá tratá-los de forma diferente. A rede WAN poderá achar que ambos os prefixos estão mais próximos dos EUA Leste do que dos EUA Oeste e, por conseguinte, encaminhar ambos os utilizadores do escritório para o circuito do ExpressRoute nos EUA Leste. No final, terá muitos utilizadores insatisfeitos no escritório de Los Angeles.

![Problema de Caso 1 do ExpressRoute - encaminhamento inferior ao ideal do cliente para a Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Solução: utilizar Comunidades do BGP
Para otimizar o encaminhamento para os utilizadores dos dois escritório, é necessário saber qual é o prefixo do Azure dos EUA Oeste e qual é o do Azure dos EUA Leste. Codificamos estas informações com os [Valores das Comunidades do BGP](expressroute-routing.md). Atribuímos um valor exclusivo de Comunidades do BGP a cada região do Azure, por exemplo, “12076:51004” para EUA Leste e “12076:51006” para EUA Oeste. Agora que sabe que prefixo corresponde a que região do Azure, já pode configurar que circuito do ExpressRoute deve ser o preferencial. Uma vez que utilizamos o BGP para trocar informações de encaminhamento, pode utilizar a Preferência Local do BGP para influenciar o encaminhamento. No nosso exemplo, pode atribuir um valor de preferência local mais alto a 13.100.0.0/16 nos EUA Oeste do que nos EUA Leste, e, da mesma forma, um valor de preferência local mais alto a 23.100.0.0/16 nos EUA Leste do que nos EUA Oeste. Esta configuração permitirá garantir que, quando estiverem disponíveis os dois caminhos para Microsoft, os utilizadores em Los Angeles optam pelo circuito do ExpressRoute nos EUA Oeste para se ligarem ao Azure dos EUA Oeste, ao passo que os utilizadores em Nova Iorque optarão pelo ExpressRoute nos EUA Leste para o Azure dos EUA Leste. O encaminhamento fica otimizado em ambos os lados. 

![Solução do Caso 1 do ExpressRoute - utilizar Comunidades do BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Utilizando a Preferência Local, pode ser aplicada a mesma técnica ao encaminhamento do cliente para a Rede Virtual do Azure. O valor da Comunidade do BGP não é etiquetado de acordo com os prefixos anunciados do Azure para a sua rede. No entanto, como sabe quais das suas implementações da Rede Virtual estão perto de que escritórios, pode configurar os seus routers em conformidade, de modo a dar preferência a um circuito do ExpressRoute face a outro.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Encaminhamento inferior ao ideal da Microsoft para o cliente
Veja outro exemplo em que as ligações da Microsoft optam por um caminho mais longo para chegarem à sua rede. Neste caso, está a utilizar servidores do Exchange no local e o Exchange Online num [ambiente híbrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Os seus escritórios estão ligados a uma WAN. Anuncia os prefixos dos servidores no local em ambos os escritórios para a Microsoft através de dois circuitos do ExpressRoute. O Exchange Online irá iniciar ligações aos servidores no local em casos como a migração da caixa de correio. Infelizmente, a ligação ao seu escritório em Los Angeles é encaminhada para o circuito do ExpressRoute nos EUA Leste, atravessando todo o continente antes de voltar à costa oeste. A causa do problema é semelhante à anterior. Sem sugestões, a rede da Microsoft não sabe qual dos prefixos do cliente está próximo dos EUA Leste e qual deles está próximo dos EUA Oeste. E escolhe o caminho errado para o seu escritório em Los Angeles.

![Caso 2 do ExpressRoute - encaminhamento inferior ao ideal da Microsoft para o cliente](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Solução: utilizar prefixação COMO CAMINHO
Existem duas soluções para o problema. A primeira é simplesmente anunciar o prefixo no local para o escritório de Los Angeles, 177.2.0.0/31, no circuito do ExpressRoute dos EUA Oeste e o prefixo no local para o escritório de Nova Iorque, 177.2.0.2/31, no circuito do ExpressRoute nos EUA Leste. Sendo assim, existe apenas um caminho para a Microsoft estabelecer ligação a cada um dos seus escritórios. Não há ambiguidade e o encaminhamento é otimizado. Com esta estrutura, tem de pensar sobre a sua estratégia de ativação pós-falha. No caso do caminho para a Microsoft via ExpressRoute ser interrompido, tem de certificar-se de que o Exchange Online ainda consegue ligar-se aos seus servidores no local. 

A segunda solução é continuar a anunciar ambos os prefixos em ambos os circuitos do ExpressRoute e, além disso, dar-nos uma sugestão de qual é o prefixo que está próximo de qual dos seus escritórios. Uma vez que suportamos a prefixação COMO Caminho do BGP, pode configurá-la para o seu prefixo de modo a influenciar o encaminhamento. Neste exemplo, pode aumentar a prefixação COMO Caminho para 172.2.0.0/31 nos EUA Leste de modo a preferirmos o circuito do ExpressRoute nos EUA Oeste para o tráfego destinado a este prefixo (uma vez que a nossa rede irá considerar o caminho para este prefixo mais curto no Oeste). Pode, do mesmo modo, aumentar a prefixação COMO Caminho para 172.2.0.2/31 nos EUA Oeste, de modo a preferirmos o circuito do ExpressRoute nos EUA Leste. O encaminhamento fica otimizado para ambos os escritórios. Com esta estrutura, se um circuito do ExpressRoute for interrompido, o Exchange Online ainda consegue contactá-lo através de outro circuito do ExpressRoute e da sua WAN. 

> [!IMPORTANT]
> Removemos os números privados COMO no COMO Caminho para os prefixos recebidos no Peering da Microsoft. Temos de acrescentar números públicos COMO no COMO Caminho de modo a influenciar o encaminhamento para o Peering da Microsoft.
> 
> 

![Solução do Caso 2 do ExpressRoute - utilizar prefixação COMO CAMINHO](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Apesar de os exemplos apresentados aqui se referirem à Microsoft e aos Peerings públicos, também suportamos as mesmas funções no Peering privado. Além disso, a prefixação COMO Caminho funciona dentro de um circuito único do ExpressRoute para influenciar a seleção dos caminhos primários e secundários.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Encaminhamento inferior ao ideal entre redes virtuais
Com o ExpressRoute, pode permitir a comunicação de Rede Virtual para Rede Virtual (também conhecida como “VNet”), ao ligá-las a um circuito do ExpressRoute. Quando as liga a vários circuitos do ExpressRoute, o encaminhamento entre as VNets pode ter um desempenho inferior ao ideal. Vamos considerar um exemplo. Tem dois circuitos do ExpressRoute, um nos E.U.A. Oeste e outro nos E.U.A. Leste. Em cada região, tem duas VNets. Os seus servidores Web estão implementados numa VNet e os servidores de aplicações noutra. Para redundância, liga as duas VNets em cada região aos circuitos local e remoto do ExpressRoute. Como pode ver abaixo, existem dois caminhos de cada VNet para a outra. As VNets não sabem que circuito do ExpressRoute é o local ou o remoto. Consequentemente, como fazem o encaminhamento Equal-Cost-Multi-Path (ECMP) para efetuar o balanceamento de carga do tráfego inter-VNet, alguns fluxos de tráfego vão seguir o caminho mais longo e ser encaminhados no circuito local do ExpressRoute.

![Caso 3 do ExpressRoute - encaminhamento inferior ao ideal entre redes virtuais](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Solução: atribuir uma ponderação elevada à ligação no local
A solução é simples. Uma vez que sabe onde estão as VNets e os circuitos, pode dizer-nos que caminho cada VNet deve preferir. Mais concretamente para este exemplo, vai atribuir uma ponderação mais elevada à ligação local do que à remota (veja o exemplo de configuração [aqui](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Quando uma VNet recebe o prefixo da outra VNet em várias ligações, vai dar preferência à ligação que tem a ponderação mais elevada para enviar o tráfego destinado a esse prefixo.

![Solução do Caso 3 do ExpressRoute - atribuir uma ponderação elevada à ligação no local](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Também pode influenciar o encaminhamento da VNet para a sua rede no local, se tiver diversos circuitos do ExpressRoute, ao configurar a ponderação de uma ligação em vez de aplicar a prefixação COMO CAMINHO, uma técnica que foi descrita no segundo cenário, acima. Para cada prefixo, para decidir como enviar o tráfego, vamos sempre considerar primeiro a ponderação da ligação face ao comprimento de COMO CAMINHO.
>
>
