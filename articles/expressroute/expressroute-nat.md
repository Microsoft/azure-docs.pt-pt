---
title: 'Azure ExpressRoute: requisitos de NAT para circuitos'
description: Esta página fornece os requisitos detalhados para configurar e gerir o NAT para circuitos do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569650"
---
# <a name="expressroute-nat-requirements"></a>Requisitos do NAT do ExpressRoute
Para ligar aos serviços em nuvem da Microsoft com o ExpressRoute, terá de configurar e gerir os NATs. Alguns fornecedores de conectividade oferecem a configuração e a gestão do NAT como um serviço gerido. Contacte o seu fornecedor de conectividade para ver se oferece tal serviço. Se não for possível, terá de cumprir os requisitos descritos abaixo. 

Reveja a página [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md) para obter uma descrição geral de vários domínios de encaminhamento. Para cumprir os requisitos públicos de endereço IP para o Azure público e peering da Microsoft, recomendamos que configure o NAT entre a sua rede e a Microsoft. Esta secção fornece uma descrição detalhada da infraestrutura do NAT que tem de configurar.

## <a name="nat-requirements-for-microsoft-peering"></a>Requisitos do NAT para peering da Microsoft
O caminho de peering da Microsoft permite-lhe ligar aos serviços em nuvem da Microsoft que não são suportados através do caminho de peering público do Azure. A lista de serviços inclui serviços Microsoft 365, tais como Exchange Online, SharePoint Online e Skype for Business. A Microsoft espera suportar uma conetividade bidirecional no peering da Microsoft. O tráfego destinado aos serviços em nuvem da Microsoft tem de realizar um SNAT para endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft. O tráfego destinado à sua rede a partir dos serviços cloud da Microsoft tem de realizar um SNAT no intervalo de Internet para prevenir o [encaminhamento assimétrico](expressroute-asymmetric-routing.md). A figura abaixo fornece uma imagem de alto nível de como o NAT deve ser configurado para o espreitar da Microsoft.

![Diagrama de alto nível de como o NAT deve ser configurado para o espreitar da Microsoft.](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Tráfego com origem na sua rede destinado à Microsoft
* Deve garantir que o tráfego está a entrar no caminho de peering da Microsoft com um endereço IPv4 público válido. A Microsoft deve conseguir validar o proprietário do conjunto de endereços IPv4 NAT num registo de Internet de encaminhamento regional (RIR) ou num registo de encaminhamento de Internet (IRR). Será efetuada uma verificação com base no número AS com o qual está a ser emparelhado e os endereços IP utilizados para o NAT. Veja a página [Requisitos do encaminhamento do ExpressRoute](expressroute-routing.md) para obter informações sobre os registos do encaminhamento.
* Os endereços IP utilizados para a configuração do peering público Azure e outros circuitos ExpressRoute não devem ser anunciados à Microsoft através da sessão do BGP. Não existe qualquer restrição ao comprimento do prefixo do IP do NAT anunciado através deste peering.
  
  > [!IMPORTANT]
  > O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Tráfego com origem na Microsoft destinado à sua rede
* Determinados cenários requerem que a Microsoft inicie a conectividade com pontos finais de serviço alojados na sua rede. Um exemplo típico do cenário seria a conectividade com os servidores ADFS alojados na sua rede a partir da Microsoft 365. Nestes casos, deve fornecer prefixos apropriados da sua rede para o peering da Microsoft. 
* É necessário realizar SNAT de tráfego Microsoft para o intervalo de Internet dos pontos finais do serviço na rede de modo a impedir o [encaminhamento assimétrico](expressroute-asymmetric-routing.md). Pedidos **e respostas** com um IP de destino que correspondem a uma rota ExpressRoute serão sempre enviados por ExpressRoute. O encaminhamento assimétrico existe se o pedido for recebido através da Internet com a resposta enviada por ExpressRoute. A entrada de SNAT de tráfego Microsoft no intervalo de Internet força o tráfego de resposta de volta para o intervalo de Internet, resolvendo o problema.

![Encaminhamento assimétrico com o ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Requisitos do NAT para o peering público Azure

> [!NOTE]
> O azure de espreitar o público não está disponível para novos circuitos.
> 

O caminho de peering público Azure permite-lhe ligar a todos os serviços alojados no Azure ao longo dos respetivos endereços IP públicos. Estes incluem os serviços listados nas [FAQ do ExpressRoute](expressroute-faqs.md) e quaisquer serviços alojados pelos ISVs no Microsoft Azure. 

> [!IMPORTANT]
> A conectividade aos serviços do Microsoft Azure em peering público é sempre iniciada a partir da sua rede para a rede da Microsoft. Por conseguinte, as sessões não podem iniciar a partir de serviços do Microsoft Azure para a sua rede através do ExpressRoute. Se tentou, os pacotes enviados para estes IPs anunciados irão utilizar a internet, em vez do ExpressRoute.
> 

O tráfego destinado ao Microsoft Azure em peering público tem de realizar um SNAT para endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft. A figura abaixo fornece uma imagem de alto nível da forma como o NAT foi configurado para cumprir o requisito acima.

![Diagrama de alto nível de como o NAT poderia ser configurado para ser SNATed para endereços IPv4 públicos válidos antes de entrarem na rede microsoft.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Conjunto IP do NAT e anúncios de rota
Deve garantir que o tráfego está a entrar no caminho de peering público Azure com um endereço IPv4 público válido. A Microsoft deve conseguir validar a propriedade do conjunto de endereços IPv4 NAT num registo de Internet de encaminhamento regional (RIR) ou num registo de encaminhamento de Internet (IRR). Será efetuada uma verificação com base no número AS com o qual está a ser emparelhado e os endereços IP utilizados para o NAT. Veja a página [Requisitos do encaminhamento do ExpressRoute](expressroute-routing.md) para obter informações sobre os registos do encaminhamento.

Não existem restrições ao comprimento do prefixo do IP do NAT anunciado através deste peering. Monitorize o conjunto NAT e confira que não fica sem sessões NAT.

> [!IMPORTANT]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Veja os requisitos para [Encaminhamento](expressroute-routing.md) e [QoS](expressroute-qos.md).
* Para obter informações sobre o fluxo de trabalho, veja [Circuito ExpressRoute aprovisiona fluxos de trabalho e estados de circuitos](expressroute-workflows.md).
* Configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configure o encaminhamento](expressroute-howto-routing-portal-resource-manager.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)

