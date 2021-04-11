---
title: Sobre o design do Azure VPN Gateway
description: Saiba como pode desenhar uma topologia de gateway VPN para ligar às redes virtuais Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99b8cdbb1528caefd30fb11c9702dff1e817c0ab
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061813"
---
# <a name="vpn-gateway-design"></a>Design VPN Gateway

É importante saber que existem configurações diferentes disponíveis para as ligações de gateway VPN. Deve determinar qual das configurações se adequa melhor às suas necessidades. Nas secções abaixo, pode ver informações de design e diagramas de topologia sobre as seguintes ligações de gateway VPN. Utilize os diagramas e as descrições para ajudar a selecionar a topologia de ligação para corresponder aos requisitos. Os diagramas mostram as principais topologias de base, mas é possível construir configurações mais complexas usando os diagramas como diretrizes.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Rede Site a Site e Multilocal (túnel VPN IPsec/IKE)

### <a name="site-to-site"></a><a name="S2S"></a>Site-a-Site

Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). As ligações S2S podem ser utilizadas para configurações em vários locais e híbridas. Uma ligação S2S requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído a ele. Para obter informações acerca da seleção de um dispositivo VPN, veja as [FAQs do Gateway de VPN - dispositivos VPN](vpn-gateway-vpn-faq.md#s2s).

![Exemplo de ligação Site a Site de Gateway de VPN do Azure](./media/design/vpngateway-site-to-site-connection-diagram.png)

O Gateway VPN pode ser configurado em modo de espera ativo utilizando um IP público ou em modo ativo usando dois IPs públicos. Em modo de espera ativo, um túnel IPsec está ativo e o outro túnel está em modo de espera. Nesta configuração, o tráfego flui através do túnel ativo, e se algum problema acontece com este túnel, o tráfego passa para o túnel de espera. *Recomenda-se* a criação do Gateway VPN em modo ativo, no qual ambos os túneis IPsec estão simultaneamente ativos, com dados a fluir em ambos os túneis ao mesmo tempo. Uma vantagem adicional do modo ativo é que os clientes experimentam posições mais elevadas.

### <a name="multi-site"></a><a name="Multi"></a>Multi-Site

Este tipo de ligação é uma variação da ligação Rede de VPNs. Cria mais de uma ligação de VPN a partir do gateway de rede virtual, ligando, geralmente, a vários sites no local. Quando trabalha com várias ligações, tem de utilizar um tipo de VPN RouteBased (conhecido como gateway dinâmico ao trabalhar com VNets clássicas). Uma vez que cada rede virtual só pode ter um gateway de VPN, todas as ligações através do mesmo partilham a largura de banda disponível. Este tipo de ligação é frequentemente designado ligação "multilocal".

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Métodos de implementação e métodos de Site para Site e Multilocal

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>VPN ponto-a-local

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet.

Ao contrário das ligações S2S, as ligações P2S não requerem um endereço IP destinado ao público ou um dispositivo VPN no local. As ligações P2S podem ser utilizadas com as ligações S2S através do mesmo gateway de VPN, desde que todos os requisitos de configuração para ambas as ligações sejam compatíveis. Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).

![Exemplo de ligação Ponto a Site de Gateway de VPN do Azure](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Métodos e modelos de implementação para P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Ligações VNet a VNet (túnel VPN IPsec/IKE)

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações de ligação multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

As VNets que liga podem estar:

* nas mesmas regiões ou em diferentes
* nas mesmas subscrições ou em diferentes 
* nos mesmos modelos de implementação ou em diferentes

![Exemplo de ligação VNet a VNet de Gateway de VPN do Azure](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Ligações entre modelos de implementação

Atualmente, o Azure tem dois modelos de implementação: clássico e Resource Manager. Se tem utilizado o Azure durante algum tempo, provavelmente as VMs do Azure e as funções de instância estão em execução numa VNet clássica. As VMs mais recentes e as instâncias da função poderão estar em execução numa VNet criada no Resource Manager. Pode criar uma ligação entre as VNets para permitir que os recursos numa VNet comuniquem diretamente com recursos de outra.

### <a name="vnet-peering"></a>VNet peering

Poderá utilizar o VNet peering para criar a ligação, desde que a rede virtual cumpra determinados requisitos. O VNet peering não utiliza um gateway de rede virtual. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementação e métodos para VNet para VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (ligação privada)

O ExpressRoute permite-lhe expandir as redes no local para a Microsoft Cloud através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços de cloud da Microsoft, tais como Microsoft Azure, Microsoft 365 e CRM Online. A conectividade pode ser a partir de uma rede any-to-any (VPN IP), uma rede Ethernet ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa instalação de colocalização.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

As ligações do ExpressRoute utilizam um gateway de rede virtual como parte da configuração necessária. Numa ligação ExpressRoute, o gateway de rede virtual está configurado com o tipo de gateway "ExpressRoute", em vez de "Vpn". Embora o tráfego que circula através de um circuito do ExpressRoute não seja encriptado por predefinição, é possível criar uma solução que permita enviar tráfego encriptado através de um circuito do ExpressRoute. Para obter mais informações sobre o ExpressRoute, veja a [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Ligações Site a Site e ExpressRoute coexistentes

O ExpressRoute é uma ligação privada direta a partir da WAN (e não através da Internet pública) para os Serviços Microsoft, incluindo o Azure. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Poder configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens.

Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. Repare que esta configuração requer dois gateways de rede virtual para a mesma rede virtual, um que utilize o tipo de gateway “Vpn” e o outro o tipo de gateway “ExpressRoute”.

![Exemplo de ligações coexistentes do ExpressRoute e Gateway de VPN](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Métodos e modelos de implementação para S2S e para ExpressRoute coexistirem

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Ligações altamente disponíveis

Para planeamento e conceção de ligações altamente disponíveis, consulte [ligações altamente disponíveis.](vpn-gateway-highlyavailable.md)

## <a name="next-steps"></a>Passos seguintes

* Veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações.

* Saiba mais sobre [as definições de configuração do Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

* Para considerações de BGP da VPN Gateway, consulte [Sobre o BGP](vpn-gateway-bgp-overview.md).

* Veja [Subscription and service limits (Subscrições e limites do serviço)](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
