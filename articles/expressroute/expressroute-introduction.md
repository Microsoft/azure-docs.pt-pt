---
title: 'Visão geral do Azure ExpressRoute: Conecte-se sobre uma ligação privada'
description: A Descrição Geral Técnica do ExpressRoute explica como funciona uma ligação do ExpressRoute para expandir a sua rede no local para o Azure através de uma ligação privada.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: 40d84a4196a3cc104a29b4b9511bd627f3f8d40e
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566879"
---
# <a name="what-is-azure-expressroute"></a>O que é o Azure ExpressRoute?
O ExpressRoute permite-lhe expandir as redes no local para a Microsoft Cloud através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços de cloud da Microsoft, como o Microsoft Azure e o Microsoft 365.

A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta. As ligações do ExpressRoute não passam para a Internet pública. Isto permite que as ligações ExpressRoute ofereçam mais fiabilidade, velocidades mais rápidas, latências consistentes e maior segurança do que as ligações típicas através da Internet. Para obter informações sobre como ligar a sua rede à Microsoft com o ExpressRoute, consulte [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).

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
Cada circuito ExpressRoute consiste em duas ligações a dois routers de borda microsoft Enterprise (MSEEs) numa [Localização ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-locations#expressroute-locations) a partir do fornecedor de conectividade/borda da sua rede. A Microsoft necessita de uma ligação BGP dupla a partir do fornecedor de conectividade/periferia da sua rede – um para cada MSEE. Pode optar por não implementar dispositivos redundantes/circuitos Ethernet na sua parte. No entanto, os fornecedores de conectividade utilizam dispositivos redundantes para verificar que as ligações são entregues à Microsoft de forma redundante. Uma configuração de conectividade redundante de Camada 3 é um requisito para que o nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços da Microsoft Cloud
As ligações ExpressRoute permitem o acesso aos seguintes serviços:
* Serviços do Microsoft Azure
* Serviços Microsoft 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Visite a página [FAQ do ExpressRoute](expressroute-faqs.md) para obter uma lista detalhada dos serviços suportados através do ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade a todas as regiões numa região geopolítica
Pode ligar-se à Microsoft a partir de uma das nossas [localizações de peering](expressroute-locations.md) e aceder a todas as regiões numa região geopolítica.

Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços cloud da Microsoft alojados na Europa do Norte e na Europa Ocidental. Consulte o artigo [Parceiros ExpressRoute e localizações peering](expressroute-locations.md) para obter uma descrição geral das regiões geopolíticas, as regiões associadas na nuvem Microsoft, e as localizações de peering ExpressRoute correspondentes.

### <a name="global-connectivity-with-expressroute-premium"></a>Conectividade global com ExpressRoute Premium
Pode permitir ao [ExpressRoute Premium](expressroute-faqs.md) alargar a conectividade através das fronteiras geopolíticas. Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços cloud da Microsoft alojados em todas as regiões pelo mundo (exceto clouds nacionais). Pode aceder aos serviços implementados na América do Sul ou Austrália da mesma forma que acede às regiões da Europa do Norte e Europa Ocidental.

### <a name="local-connectivity-with-expressroute-local"></a>Conectividade local com ExpressRoute Local
Pode transferir dados de forma eficaz, permitindo ao [SKU local](expressroute-faqs.md) se conseguir levar os seus dados para uma localização ExpressRoute perto da região de Azure desejada. Com o Local, a transferência de dados está incluída na taxa portuária ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Na conectividade local com Alcance Global do ExpressRoute
Pode ativar o Alcance Global do ExpressRoute para trocar dados entre os sites no local ao ligar os seus circuitos do ExpressRoute. Por exemplo, se tiver um datacenter privado na Califórnia ligado ao ExpressRoute em Silicon Valley e outro datacenter privado no Texas ligado ao ExpressRoute em Dallas, com o Alcance Global do ExpressRoute, pode ligar os datacenters privados em conjunto através de dois circuitos do ExpressRoute. O tráfego em vários datacenters irá percorrer a rede da Microsoft.

Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema de parceiro de conectividade avançada
O ExpressRoute tem um crescente ecossistema de fornecedores de conectividade e parceiros integradores de sistemas. Para obter as informações mais recentes, veja [Parceiros e localizações de peering do ExpressRoute](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft funciona em ambientes de nuvem isolados para regiões geopolíticas especiais e segmentos de cliente. Veja a página [Parceiros e localizações de peering do ExpressRoute](expressroute-locations.md) para obter uma lista de clouds e fornecedores nacionais.

### <a name="expressroute-direct"></a>ExpressRoute Direct
O ExpressRoute Direct permite aos clientes ligarem diretamente à rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. O ExpressRoute Direct dispõe de conectividade de 100 Gbps dupla, que suporta conetividade Ativa/Ativa em escala.

As principais funcionalidades que o ExpressRoute Direct dispõe incluem, mas não se limitam a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para setores que são regulamentados e exigem conectividade dedicada e isolada como: Bancos, Governo e Retalho
* Controlo granular de distribuição do circuito baseado na unidade de negócios

Para obter mais informações, veja [Sobre o ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Opções de largura de banda
Pode comprar circuitos ExpressRoute para uma vasta gama de larguras de banda. As larguras de banda suportadas são apresentadas abaixo. Confirme que consulta o seu fornecedor de conectividade para determinar as larguras de banda suportadas que suportam.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico da largura de banda
Pode aumentar a largura de banda do circuito ExpressRoute (na base de melhor esforço) sem ter de fechar as suas ligações. Para obter mais informações, veja [Modificar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Modelos de faturação flexíveis
Pode escolher um modelo de faturação que funciona melhor para si. Escolha entre os modelos de faturação listados abaixo. Para obter mais informações, veja [FAQ do ExpressRoute](expressroute-faqs.md).

* **Dados ilimitados**. A faturação é baseada numa taxa mensal; todas as transferências de dados de entrada e saída estão incluídas de forma gratuita.
* **Dados limitados**. A faturação é baseada numa taxa mensal; todas as transferências de dados de entrada são gratuitas. A transferência de dados de saída é cobrada por GB da transferência de dados. As taxas da transferência de dados variam consoante a região.
* **Suplemento do ExpressRoute Premium**. O ExpressRoute Premium é um suplemento do circuito do ExpressRoute. O suplemento do ExpressRoute Premium oferece as seguintes capacidades: 
  * Aumento dos limites de rota para peering público e privado Azure de 4000 rotas para 10 000 rotas.
  * Conectividade global para os serviços. Um circuito ExpressRoute criado em qualquer região (excluindo as nuvens nacionais) terá acesso aos recursos a partir de qualquer outra região do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acedida através de um circuito ExpressRoute aprovisionado em Silicon Valley.
  * Aumento do número de ligações VNet por circuito ExpressRoute, de 10 até um limite superior, dependendo da largura de banda do circuito.

## <a name="faq"></a>FAQ
Para perguntas mais frequentes sobre o ExpressRoute, veja [FAQ do ExpressRoute](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>O que há de novo?

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade ExpressRoute na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [ExpressRoute connectivity models (Modelos de conectividade do ExpressRoute)](expressroute-connectivity-models.md).
* Saiba mais sobre ligações ExpressRoute e domínios de encaminhamento. Veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Encontre um fornecedor de serviços. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Veja os requisitos para [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Criar e modificar o peering de um circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Ligar uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
