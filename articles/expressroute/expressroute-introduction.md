---
title: 'Visão geral do Azure ExpressRoute: Conecte-se sobre uma ligação privada'
description: A Descrição Geral Técnica do ExpressRoute explica como funciona uma ligação do ExpressRoute para expandir a sua rede no local para o Azure através de uma ligação privada.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206956"
---
# <a name="what-is-azure-expressroute"></a>O que é o Azure ExpressRoute?
O ExpressRoute permite-lhe estender as suas redes no local para a nuvem da Microsoft através de uma ligação privada com a ajuda de um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços de cloud da Microsoft, como o Microsoft Azure e o Microsoft 365.

A conectividade pode ser a partir de uma rede de qualquer para qualquer (IP VPN), uma rede Ethernet ponto a ponto, ou uma ligação cruzada virtual através de um provedor de conectividade em uma instalação de coinstalação. As ligações ExpressRoute não passam pela Internet pública. Isto permite que as ligações ExpressRoute ofereçam mais fiabilidade, velocidades mais rápidas, latências consistentes e maior segurança do que as ligações típicas através da Internet. Para obter informações sobre como ligar a sua rede à Microsoft com o ExpressRoute, consulte [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).

![Visão geral da ligação ExpressRoute](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Principais vantagens

* A conectividade da Camada 3 entre a sua rede no local e o Microsoft Cloud através de um fornecedor de conectividade. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma ligação Ethernet ponto a ponto ou através de uma ligação cruzada virtual por meio de uma troca de Ethernet.
* Conectividade de serviços em nuvem da Microsoft em todas as regiões na região geopolítica.
* Conectividade global com os serviços da Microsoft em todas as regiões com o suplemento ExpressRoute Premium.
* Encaminhamento dinâmico entre a rede e a Microsoft através do BGP.
* Redundância incorporada em cada localização de peering para maior fiabilidade.
* Tempo de atividade da ligação [SLA](https://azure.microsoft.com/support/legal/sla/).
* Suporte QoS para o Skype para empresas.

Para obter mais informações, consulte as [ExpressRoute FAQ (FAQs do ExpressRoute)](expressroute-faqs.md).

## <a name="features"></a>Funcionalidades

### <a name="layer-3-connectivity"></a>Conectividade de Camada 3
A Microsoft utiliza o BGP, um protocolo de encaminhamento dinâmico padrão da indústria para trocar rotas entre a sua rede no local, as instâncias no Azure e os endereços públicos da Microsoft. Estabelecemos várias sessões BGP com a sua rede para diferentes perfis de tráfego. Pode obter mais detalhes no artigo [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundância
Cada circuito ExpressRoute consiste em duas ligações a dois routers de borda microsoft Enterprise (MSEEs) numa [Localização ExpressRoute](./expressroute-locations.md#expressroute-locations) a partir do fornecedor de conectividade/borda da sua rede. A Microsoft necessita de uma ligação BGP dupla a partir do fornecedor de conectividade/periferia da sua rede – um para cada MSEE. Pode optar por não implementar dispositivos redundantes/circuitos Ethernet na sua parte. No entanto, os fornecedores de conectividade utilizam dispositivos redundantes para verificar que as ligações são entregues à Microsoft de forma redundante. Uma configuração de conectividade redundante de Camada 3 é um requisito para que o nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços da Microsoft Cloud
As ligações ExpressRoute permitem o acesso aos seguintes serviços:
* Serviços do Microsoft Azure
* Serviços do Microsoft 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Visite a página [FAQ do ExpressRoute](expressroute-faqs.md) para obter uma lista detalhada dos serviços suportados através do ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade a todas as regiões numa região geopolítica
Pode ligar-se à Microsoft a partir de uma das nossas [localizações de peering](expressroute-locations.md) e aceder a todas as regiões numa região geopolítica.

Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute. Você terá acesso a todos os serviços de cloud da Microsoft hospedados na Europa Do Norte e Oeste. Consulte o artigo [Parceiros ExpressRoute e localizações peering](expressroute-locations.md) para obter uma descrição geral das regiões geopolíticas, as regiões associadas na nuvem Microsoft, e as localizações de peering ExpressRoute correspondentes.

### <a name="global-connectivity-with-expressroute-premium"></a>Conectividade global com ExpressRoute Premium
Pode permitir ao [ExpressRoute Premium](expressroute-faqs.md) alargar a conectividade através das fronteiras geopolíticas. Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute terá acesso a todos os serviços de cloud da Microsoft hospedados em todas as regiões do mundo. Também pode aceder a serviços implantados na América do Sul ou na Austrália da mesma forma que acede às regiões do Norte e da Europa Ocidental. As nuvens nacionais estão excluídas.

### <a name="local-connectivity-with-expressroute-local"></a>Conectividade local com ExpressRoute Local
Pode transferir dados de forma eficaz, permitindo o [SKU Local.](expressroute-faqs.md) Com o SKU local, pode levar os seus dados para uma localização ExpressRoute perto da região Azure que deseja. Com o Local, a transferência de dados está incluída na taxa portuária ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Na conectividade local com Alcance Global do ExpressRoute
Pode ativar o Alcance Global do ExpressRoute para trocar dados entre os sites no local ao ligar os seus circuitos do ExpressRoute. Por exemplo, se tiver um centro de dados privado na Califórnia ligado a um circuito ExpressRoute em Silicon Valley e a outro centro de dados privado no Texas ligado a um circuito ExpressRoute em Dallas. Com o ExpressRoute Global Reach, pode ligar os seus centros de dados privados através destes dois circuitos ExpressRoute. O tráfego em vários datacenters irá percorrer a rede da Microsoft.

Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema de parceiro de conectividade avançada
O ExpressRoute tem um crescente ecossistema de fornecedores de conectividade e parceiros integradores de sistemas. Para obter as informações mais recentes, veja [Parceiros e localizações de peering do ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft funciona em ambientes de nuvem isolados para regiões geopolíticas especiais e segmentos de cliente. Veja a página [Parceiros e localizações de peering do ExpressRoute](expressroute-locations.md) para obter uma lista de clouds e fornecedores nacionais.

### <a name="expressroute-direct"></a>ExpressRoute Direct
O ExpressRoute Direct permite aos clientes ligarem diretamente à rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. O ExpressRoute Direct fornece conectividade dupla de 100 Gbps, que suporta a conectividade Ative/Ative à escala.

As principais funcionalidades que o ExpressRoute Direct fornece incluem, mas não se limitam a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para setores que são regulamentados e exigem conectividade dedicada e isolada como: Bancos, Governo e Retalho
* Controlo granular de distribuição do circuito baseado na unidade de negócios

Para obter mais informações, veja [Sobre o ExpressRoute Direct](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Opções de largura de banda
Pode comprar circuitos ExpressRoute para uma vasta gama de larguras de banda. As larguras de banda suportadas são listadas como seguidas. Confirme que consulta o seu fornecedor de conectividade para determinar as larguras de banda suportadas que suportam.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico da largura de banda
Pode aumentar a largura de banda do circuito ExpressRoute (na base de melhor esforço) sem ter de fechar as suas ligações. Para obter mais informações, consulte [modificar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Modelos de faturação flexíveis
Pode escolher um modelo de faturação que funciona melhor para si. Escolha entre os modelos de faturação listados como seguidos. Para obter mais informações, veja [FAQ do ExpressRoute](expressroute-faqs.md).

* **Dados ilimitados**. A faturação é baseada numa taxa mensal; todas as transferências de dados de entrada e saída estão incluídas de forma gratuita.
* **Dados limitados**. A faturação é baseada numa taxa mensal; todas as transferências de dados de entrada são gratuitas. A transferência de dados de saída é cobrada por GB da transferência de dados. As taxas da transferência de dados variam consoante a região.
* **Suplemento do ExpressRoute Premium**. O ExpressRoute Premium é um suplemento do circuito do ExpressRoute. O suplemento do ExpressRoute Premium oferece as seguintes capacidades: 
  * Aumento dos limites de rota para peering público e privado Azure de 4000 rotas para 10 000 rotas.
  * Conectividade global para os serviços. Um circuito ExpressRoute criado em qualquer região (excluindo nuvens nacionais) terá acesso a recursos em todas as outras regiões do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acedida através de um circuito ExpressRoute aprovisionado em Silicon Valley.
  * Aumento do número de ligações VNet por circuito ExpressRoute, de 10 até um limite superior, dependendo da largura de banda do circuito.

## <a name="faq"></a>FAQ
Para perguntas mais frequentes sobre o ExpressRoute, veja [FAQ do ExpressRoute](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Novidades

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade ExpressRoute na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute)

## <a name="next-steps"></a>Passos seguintes
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Saiba mais sobre [ExpressRoute connectivity models (Modelos de conectividade do ExpressRoute)](expressroute-connectivity-models.md).
* Encontre um fornecedor de serviços. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).