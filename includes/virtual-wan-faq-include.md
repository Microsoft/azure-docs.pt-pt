---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98ea4d78a473123708be6e371587252acad6ffcd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554788"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um vpngateway de WAN Virtual do Azure?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. As funcionalidades de conectividade do ExpressRoute e de ponto a site estão atualmente em Pré-visualização. Os dispositivos de ramo CPE fazem o aprovisionamento automático e ligam numa WAN Virtual do Azure. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Veja a [Lista de Parceiros Preferenciais](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Que fornecedores de dispositivos (parceiros de WAN Virtual) são suportados na altura do lançamento? 

Atualmente, muitos parceiros suportam a experiência de WAN Virtual totalmente automatizada. Para obter mais informações, veja [Parceiros de WAN Virtual](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são os passos de automatização dos parceiros da WAN Virtual?

Para obter os passos de automatização, veja [Automatização dos parceiros da WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como é que os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

As soluções de conectividade definida pelo software gerem, normalmente, os respetivos dispositivos de ramo com um controlador ou um centro de aprovisionamento de dispositivos. O controlador pode utilizar APIs do Azure para automatizar a conectividade à WAN Virtual do Azure. Para mais informações, veja Automatização de parceiro de WAN Virtual.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera funcionalidades de conectividade existentes?   

As funcionalidades de conectividade do Azure existentes não sofrem quaisquer alterações.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existem novos recursos do Resource Manager disponíveis para a WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Resource Manager. Para obter mais informações, veja a [Descrição Geral](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN se podem ligar a um único Hub?

Cada hub virtual suporta até 1000 ligações. Cada ligação consiste de dois túneis que estão numa configuração ativo-ativo. Os túneis terminam num vpngateway de Hub Virtual do Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Sim. O fluxo de tráfego ao iniciar seria do dispositivo local para o Microsoft edge mais próximo e depois para o Hub Virtual.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O peering de VNet Global é suportado com a WAN Virtual do Azure? 

 Não.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>As VNets spoke ligadas a um hub virtual podem comunicar entre si?

Sim. Falava VNets podem comunicar diretamente através do Peering de rede Virtual. No entanto, não suportamos a VNets comunicar modo transitivo através do hub. Para obter mais informações, veja [Peering de Rede Virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure. Em primeiro lugar, ligue a VNet da aplicação de rede virtual ao hub com uma ligação de Rede Virtual do Hub. Em seguida, crie uma rota do Hub Virtual com o salto seguinte a apontar para a Aplicação Virtual. Pode aplicar várias rotas à Tabela de Rotas do Hub Virtual. Quaisquer spokes ligados à VNet NVA têm de estar ligados adicionalmente ao hub virtual para garantir que as rotas de VNet do spoke são propagadas para sistemas no local.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Uma VNet de NVA pode ter um gateway de rede virtual?

Não. A VNet de NVA não pode ter um gateway de rede virtual se estiver ligada ao hub virtual. 

### <a name="is-there-support-for-bgp"></a>Existe suporte para BGP?

Sim, o BGP é suportado. Para garantir que as rotas de uma VNet de NVA são anunciadas adequadamente, os spokes têm de desativar o BGP caso estejam ligados a uma VNet de NVA que, por sua vez, esteja ligada a um hub virtual. Além disso, ligue as VNets do spoke a um hub virtual para garantir que as rotas de VNet do spoke sejam propagadas a sistemas no local.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, pode encaminhar o tráfego para uma VNet com a Tabela de Rotas do Hub Virtual.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?
 
Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Enviar um e-mail para azurevirtualwan@microsoft.com. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Pode ser criada uma configuração simples de um WAN Virtual com um hub e um vpnsite com um [Modelo de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN Virtual é principalmente um serviço orientado pelo REST ou pelo Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute. Embora o VPN site a site esteja em GA, o ExpressRoute e o ponto a site estão atualmente em Pré-visualização.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de Ramificação a Ramificação atravessa a WAN Virtual do Azure?

Sim.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Como é que a WAN Virtual é diferente do Gateway de Rede Virtual do Azure?

O VPN de Gateway de Rede Virtual está limitado a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Pode ligar-se até 1000 ligações de ramo com 2 Gbps no hub em todas as regiões, exceto na região Centro-Oeste. Para a região Centro-Oeste, estão disponíveis 20 Gbps. Estarão disponíveis 20 Gbps para mais regiões no futuro. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Pode ter um hub por região, o que significa que se pode ligar a mais de 1000 ramos nos hubs.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Esta WAN Virtual precisa do ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Utiliza a conectividade de site a site standard IPsec através de ligações da Internet a partir do dispositivo para um hub de WAN Virtual do Azure. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para Sites que estão ligados através do ExpressRoute no Hub Virtual (Sob a Pré-visualização), os sites podem ter fluxo de tráfego de ramo para ramo entre o VPN e o ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de ramos está limitado a 1000 ligações por hub/região e um total de 2 Gbps no hub. A exceção é a região EUA Centro-Oeste, que tem um total de 20 Gbps. Vamos implementar 20 Gbps para outras regiões no futuro.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo no local utilize vários ISP em paralelo ou trata-se sempre de um único túnel VPN?

Sim, pode ter túneis ativo-ativo (2 túneis = 1 ligação de WAN Virtual do Azure) de uma única ramificação, dependendo do dispositivo de ramificação.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramificação -> ISP -> Microsoft Edge -> Microsoft DC -> Microsoft Edge-> ISP -> dispositivo de ramificação

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma ligação à Internet e um dispositivo físico, preferencialmente dos nossos [parceiros](https://go.microsoft.com/fwlink/p/?linkid=2019615) integrados. Opcionalmente, pode gerir manualmente a configuração e a conectividade ao Azure a partir do seu dispositivo preferido.
