---
title: Gateway de VPN do Azure | Microsoft Docs
description: Saiba mais sobre o significado de gateway de VPN e como o pode utilizar para ligar às redes virtuais do Azure. Incluindo soluções IPsec/IKE Site a Site entre vários locais e VNet a VNet, bem como VPN Ponto a Site.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 02/22/2019
ms.author: cherylmc
ms.openlocfilehash: 7b2b5c7201fe45fb52eb333b9e32b4996e00df9b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822928"
---
# <a name="what-is-vpn-gateway"></a>O que é um Gateway de VPN?

Um gateway de VPN é um tipo específico de gateway de rede virtual utilizado para enviar tráfego encriptado entre uma rede virtual do Azure e uma localização no local através da Internet pública. Também pode utilizar um gateway de VPN para enviar tráfego encriptado entre as redes virtuais do Azure através da rede da Microsoft. Cada rede virtual pode ter apenas um gateway de VPN. No entanto, pode criar várias ligações para o mesmo gateway de VPN. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

## <a name="whatis"></a>O que é um gateway de rede virtual?

Um gateway de rede virtual é composto por duas ou mais máquinas virtuais implementadas numa sub-rede específica que criar, denominada *sub-rede do gateway*. As VMs localizadas na sub-rede do gateway são criadas quando criar o gateway de rede virtual. As VMs do gateway de rede virtual são configuradas para conter tabelas de encaminhamento e serviços de gateway específicos do gateway. Não pode configurar diretamente as VMs que fazem parte do gateway de rede virtual e nunca deve implementar recursos adicionais na sub-rede do gateway.

A criação de um gateway de rede virtual pode demorar até 45 minutos a concluir. Quando cria um gateway de rede virtual, as VMs do gateway são implementadas na sub-rede do gateway e configuradas com as definições que especificar. Uma das definições que configura é o tipo de gateway. O tipo de gateway "vpn" especifica que o tipo de gateway de rede virtual criado é um gateway de VPN. Depois de criar um gateway de VPN, pode criar uma ligação de túnel de VPN IPsec/IKE entre esse gateway de VPN e outro gateway de VPN (VNet a VNet) ou criar uma ligação de túnel de VPN IPsec/IKE entre vários locais entre o gateway de VPN e um dispositivo VPN no local (Site a Site). Também pode criar uma ligação VPN Ponto a Site (VPN através de IKEv2 ou SSTP), que permite ligar à sua rede virtual a partir de uma localização remota, como a partir de uma conferência ou de casa.

## <a name="configuring"></a>Configurar um Gateway de VPN

Uma ligação de gateway de VPN depende de vários recursos que estão configurados com definições específicas. A maior parte dos recursos pode ser configurada em separado, embora alguns recursos tenham de ser configurados numa determinada ordem.

### <a name="settings"></a>Definições

As definições que escolheu para cada recurso são essenciais para a criação de uma ligação com êxito. Para obter informações sobre recursos individuais e sobre definições para o Gateway de VPN, consulte [definições sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). O artigo contém informações para ajudar a compreender os tipos de gateway, os SKUs de gateway, os tipos de VPN, os tipos de ligação, as sub-redes de gateway, os gateways de rede local e várias outras definições de recursos que poderá querer considerar.

### <a name="tools"></a>Ferramentas de implementação

Pode começar por criar e configurar recursos utilizando uma ferramenta de configuração, como o portal do Azure. Mais tarde, pode decidir mudar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não pode configurar todos os recursos e definições de recursos no portal do Azure. As instruções nos artigos para cada topologia de ligação especificam quando uma ferramenta de configuração especifica é necessária. 

### <a name="models"></a>Modelo de implementação

Existem atualmente dois modelos de implementação do Azure. Ao configurar um gateway de VPN, os passos seguidos dependem do modelo de implementação que utilizou para criar a rede virtual. Por exemplo, se tiver criado a VNet com o modelo de implementação clássica, utilize as diretrizes e as instruções do modelo de implementação clássica para criar e configurar as definições do gateway de VPN. Para mais informações sobre os modelos de implementação, consulte [Compreender o Gestor de Recursos e modelos de implementação clássicos](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Tabela de planeamento

A tabela seguinte pode ajudá-lo a decidir a melhor opção de conectividade para a sua solução.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>SKUs de Gateway

Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA. Para saber mais sobre as SKUs do gateway, incluindo as funcionalidades suportadas, produção e desenvolvimento-teste, bem como passos de configuração, consulte [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Diagramas de topologia de ligação

É importante saber que existem configurações diferentes disponíveis para as ligações de gateway VPN. Deve determinar qual das configurações se adequa melhor às suas necessidades. Nas secções abaixo, pode ver as informações e diagramas de topologia sobre as seguintes ligações de gateway VPN: As secções seguintes contêm tabelas que listam:

* O modelo de implementação disponível
* As ferramentas de configuração disponíveis
* Ligações que o levam diretamente para um artigo, se disponível

Utilize os diagramas e as descrições para ajudar a selecionar a topologia de ligação para corresponder aos requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar configurações mais complexas utilizando os diagramas como orientação.

## <a name="s2smulti"></a>Rede Site a Site e Multilocal (túnel VPN IPsec/IKE)

### <a name="S2S"></a>Rede de VPN

Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). As ligações S2S podem ser utilizadas para configurações em vários locais e híbridas. A ligação S2S requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. Para obter informações acerca da seleção de um dispositivo VPN, veja as [FAQs do Gateway de VPN - dispositivos VPN](vpn-gateway-vpn-faq.md#s2s).

![Exemplo de ligação Site a Site de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multilocal

Este tipo de ligação é uma variação da ligação Rede de VPNs. Cria mais de uma ligação de VPN a partir do gateway de rede virtual, ligando, geralmente, a vários sites no local. Quando trabalha com várias ligações, tem de utilizar um tipo de VPN RouteBased (conhecido como gateway dinâmico ao trabalhar com VNets clássicas). Uma vez que cada rede virtual só pode ter um gateway de VPN, todas as ligações através do mesmo partilham a largura de banda disponível. Este tipo de ligação é frequentemente designado ligação "multilocal".

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Métodos de implementação e métodos de Site para Site e Multilocal

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Ponto a Site (VPN por IKEv2 ou SSTP)

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet.

Ao contrário das ligações S2S, as ligações P2S não requerem um endereço IP destinado ao público ou um dispositivo VPN no local. As ligações P2S podem ser utilizadas com as ligações S2S através do mesmo gateway de VPN, desde que todos os requisitos de configuração para ambas as ligações sejam compatíveis. Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).


![Exemplo de ligação Ponto a Site de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Métodos e modelos de implementação para P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Ligações VNet a VNet (túnel VPN IPsec/IKE)

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações de ligação multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

As VNets que liga podem estar:

* nas mesmas regiões ou em diferentes
* nas mesmas subscrições ou em diferentes 
* nos mesmos modelos de implementação ou em diferentes

![Exemplo de ligação VNet a VNet de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Ligações entre modelos de implementação

Atualmente, o Azure tem dois modelos de implementação: clássico e Resource Manager. Se tem utilizado o Azure durante algum tempo, provavelmente as VMs do Azure e as funções de instância estão em execução numa VNet clássica. As VMs mais recentes e as instâncias da função poderão estar em execução numa VNet criada no Resource Manager. Pode criar uma ligação entre as VNets para permitir que os recursos numa VNet comuniquem diretamente com recursos de outra.

### <a name="vnet-peering"></a>VNet peering

Poderá utilizar o VNet peering para criar a ligação, desde que a rede virtual cumpra determinados requisitos. O VNet peering não utiliza um gateway de rede virtual. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementação e métodos para VNet para VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (ligação privada)

O ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

As ligações do ExpressRoute utilizam um gateway de rede virtual como parte da configuração necessária. Numa ligação ExpressRoute, o gateway de rede virtual está configurado com o tipo de gateway "ExpressRoute", em vez de "Vpn". Embora o tráfego que circula através de um circuito do ExpressRoute não seja encriptado por predefinição, é possível criar uma solução que permita enviar tráfego encriptado através de um circuito do ExpressRoute. Para obter mais informações sobre o ExpressRoute, veja a [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Ligações Site a Site e ExpressRoute coexistentes

O ExpressRoute é uma ligação privada direta a partir da WAN (e não através da Internet pública) para os Serviços Microsoft, incluindo o Azure. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Poder configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens.

Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. Repare que esta configuração requer dois gateways de rede virtual para a mesma rede virtual, um que utilize o tipo de gateway “Vpn” e o outro o tipo de gateway “ExpressRoute”.

![Exemplo de ligações coexistentes do ExpressRoute e Gateway de VPN](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Métodos e modelos de implementação para S2S e para ExpressRoute coexistirem

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Preços

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Para obter mais informações sobre os SKUs de gateway para o Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o Gateway de Aplicação, veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Passos Seguintes

- Veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações.
- Veja [Subscription and service limits (Subscrições e limites do serviço)](../azure-subscription-service-limits.md#networking-limits).
- Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
