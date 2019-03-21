---
title: Ligar a sua rede no local para o Azure | Documentos da Microsoft
description: Explica e compara os diferentes métodos disponíveis para ligar a serviços cloud da Microsoft, como o Azure, Office 365 e Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "23851016"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Ligar a sua rede no local para o Azure
A Microsoft fornece vários tipos de serviços cloud. Enquanto pode ligar a todos os serviços na Internet pública, também pode ligar a alguns dos serviços com um túnel de rede privada virtual (VPN) através da Internet ou de uma ligação privada direta para a Microsoft. Este artigo ajuda-o a determinar que opção de conectividade atenderão melhor suas necessidades com base nos tipos de serviços cloud da Microsoft que consumir. A maioria das organizações utilizam vários tipos de ligação descritos abaixo.

## <a name="connecting-over-the-public-internet"></a>Ligar através da Internet pública
Este tipo de ligação fornece acesso a serviços cloud da Microsoft diretamente através da Internet, conforme mostrado abaixo.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Esta ligação é, normalmente, o primeiro tipo utilizado para ligar ao serviços cloud da Microsoft. A tabela abaixo lista os prós e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Não requer nenhuma modificação para a sua rede no local, desde que todos os dispositivos de cliente têm acesso ilimitado a todos os endereços IP e portas na Internet. |Embora o tráfego, muitas vezes, é encriptado através de HTTPS, ele pode ser interceptado em trânsito, uma vez que ele percorre a Internet pública. |
| Pode ligar a todos os serviços cloud da Microsoft expostos à Internet pública. |Latência imprevisível porque a ligação atravessa a Internet. |
| Utiliza a ligação à Internet existente. | |
| Não precisa de gestão de quaisquer dispositivos de conectividade. | |

Esta ligação não tem conectividade ou os custos de largura de banda, uma vez que usar a ligação à Internet existente.

## <a name="connecting-with-a-point-to-site-connection"></a>Ligar com uma ligação ponto a site
Este tipo de ligação fornece acesso a alguns serviços cloud da Microsoft através de um túnel de Secure Socket Tunneling Protocol (SSTP) através da Internet, conforme mostrado abaixo.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "ligaçãopontoasitede")

A ligação é efetuada através da ligação de Internet existente, mas requer a utilização de um Gateway de VPN do Azure. A tabela abaixo lista os prós e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Não requer nenhuma modificação para a sua rede no local, desde que todos os dispositivos de cliente têm acesso ilimitado a todos os endereços IP e portas na Internet. |Embora o tráfego é encriptado com IPSec, ele pode ser interceptado em trânsito, uma vez que ele percorre a Internet pública. |
| Utiliza a ligação à Internet existente. |Latência imprevisível porque a ligação atravessa a Internet. |
| Débito até 200 Mb/s por gateway. |Requer a criação e gestão de ligações separadas entre cada dispositivo na sua rede no local e cada gateway que precisa se conectar à cada dispositivo. |
| Pode ser utilizado para ligar aos serviços do Azure que podem ser ligados a um redes virtuais do Azure (VNet), como máquinas virtuais do Azure e serviços Cloud do Azure. |Requer uma administração mínima em curso de um Gateway de VPN do Azure. |
| Não pode ser utilizado para ligar ao Microsoft Office 365 ou Dynamics CRM Online. | |
| Não pode ser utilizado para ligar aos serviços do Azure que não não possível ligar a uma VNet. | |

Saiba mais sobre o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) serviço, sua [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)e a transferência de dados de saída [preços](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Ligar com uma ligação site a site
Este tipo de ligação fornece acesso a alguns serviços cloud da Microsoft através de um túnel IPSec através da Internet, conforme mostrado abaixo.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "ligação Site a site")

A ligação é efetuada através da ligação de Internet existente, mas requer a utilização de um Gateway de VPN do Azure com os seus preços associados e os preços da transferência de dados de saída. A tabela abaixo lista os prós e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Todos os dispositivos na sua rede no local podem comunicar com serviços do Azure ligados a uma VNet, pelo que não é necessário para configurar ligações individuais para cada dispositivo. |Embora o tráfego é encriptado com IPSec, ele pode ser interceptado em trânsito, uma vez que ele percorre a Internet pública. |
| Utiliza a ligação à Internet existente. |Latência imprevisível porque a ligação atravessa a Internet. |
| Pode ser utilizado para ligar a serviços do Azure que podem ser ligados a uma VNet como as máquinas virtuais e serviços em nuvem. |Tem de configurar e gerir um validados VPN dispositivo * no local. |
| Débito até 200 Mb/s por gateway. |Requer uma administração mínima em curso de um Gateway de VPN do Azure. |
| Pode forçar o tráfego de saída iniciado a partir de máquinas de virtuais na cloud através da rede no local para inspeção e registo através de rotas definidas pelo utilizador ou o protocolo BGP (Border Gateway) * *. |Não pode ser utilizado para ligar ao Microsoft Office 365 ou Dynamics CRM Online. |
| Não pode ser utilizado para ligar aos serviços do Azure que não não possível ligar a uma VNet. | |
| Se usar os serviços que iniciam ligações para dispositivos no local e as políticas de segurança requerem que elas, poderá ter uma firewall entre a rede no local e o Azure. | |

* * Ver uma lista dos [dispositivos VPN validados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* * * Saiba mais sobre como utilizar [rotas definidas pelo utilizador](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forçar o encaminhamento de VNets do Azure para um dispositivo no local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Ligar com uma ligação privada dedicada
Este tipo de ligação fornece acesso a todos os serviços cloud da Microsoft através de uma ligação privada dedicada para a Microsoft que atravessa a Internet, conforme mostrado abaixo.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ligação do ExpressRoute")

A ligação requer a utilização do serviço ExpressRoute e uma ligação a um fornecedor de conectividade. A tabela abaixo lista os prós e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| O tráfego não puder intercetar em trânsito através da Internet pública, uma vez que é utilizada uma ligação dedicada através de um fornecedor de serviço. |Requer a gestão de router no local. |
| Largura de banda até 10 Gb/s por circuito do ExpressRoute e de débito até 2 Gb/s para cada gateway. |Requer uma ligação dedicada para um fornecedor de conectividade. |
| Latência previsível porque se trata de uma ligação dedicada à Microsoft que atravessa a Internet. |Pode exigir uma administração mínima em curso de um ou mais Gateways de VPN do Azure (se ligar o circuito a VNets). |
| Não necessita da comunicação criptografada, embora pode criptografar o tráfego, se assim o desejar. |Se estiver a utilizar serviços em nuvem que iniciam ligações para dispositivos no local, poderá ter uma firewall entre a rede no local e o Azure. |
| Pode ligar diretamente ao todos os serviços cloud da Microsoft, com algumas exceções *. |Necessita de tradução de endereços de rede (NAT) de endereços IP no local a inserção de datacenters da Microsoft para serviços que não podem ser ligados a um VNet.* * |
| Pode forçar o tráfego de saída iniciado a partir de máquinas de virtuais na cloud através da rede no local para inspeção e registo de utilização do BGP. | |

* * Ver um [lista de serviços](../expressroute/expressroute-faqs.md#supported-services) que não pode ser utilizado com o ExpressRoute. Deve ser aprovada a sua subscrição do Azure para ligar ao Office 365.  Consulte a [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artigo para obter detalhes.
* * * Saiba mais sobre o ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisitos.

Saiba mais sobre [ExpressRoute](../expressroute/expressroute-introduction.md), está associado [preços](https://azure.microsoft.com/pricing/details/expressroute), e [fornecedores de conectividade](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Considerações adicionais
* Tem várias opções acima de vários limites máximos, que eles podem suportar para ligações de VNet, ligações de gateway e outros critérios. Recomenda-se que reveja do Azure [limites de rede](../azure-subscription-service-limits.md#networking-limits) para compreender se qualquer um deles impacto nos tipos de conectividade optar por utilizar.
* Se quiser ligar um gateway a partir de uma ligação de VPN de site a site para a mesma VNet como um gateway do ExpressRoute, deve se familiarizar com limitações importantes em primeiro lugar. Consulte a [Site a Site e ExpressRoute de configurar ligações coexistentes](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) artigo para obter mais detalhes.

## <a name="next-steps"></a>Passos Seguintes
Os recursos a seguir explicam como implementar os tipos de ligação abordados neste artigo.

* [Implementar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementar uma ligação site a site](guidance-hybrid-network-vpn.md)
* [Implementar uma ligação privada dedicada](guidance-hybrid-network-expressroute.md)
* [Implementar uma ligação privada dedicada com uma ligação site a site para elevada disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)
