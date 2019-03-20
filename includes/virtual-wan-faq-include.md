---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 931bc26e22db4bbf02a18d4824b9c846f1e66b18
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190690"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um vpngateway de WAN Virtual do Azure?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. As funcionalidades de conectividade do ExpressRoute e de ponto a site estão atualmente em Pré-visualização. CPE ramificação autoprovision de dispositivos e ligar numa WAN Virtual do Azure. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [preferencial a lista de parceiros](https://go.microsoft.com/fwlink/p/?linkid=2019615).

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

Sim, o BGP é suportado. Quando cria um site VPN, pode fornecer os parâmetros BGP no mesmo. Isto irá implica que todas as ligações criadas no Azure para esse site serão ativadas para BGP. Além disso, se tivesse uma VNet com uma NVA, e se esta VNet NVA foi ligado a um concentrador de Virtual WAN, para garantir que as rotas de uma VNet NVA são anunciadas apropriadamente, spokes que estejam anexados a NVA VNet devem desativar o BGP. Além disso, ligar esses spoke VNets para o hub virtual VNet para garantir que as rotas de VNet spoke sejam propagadas para sistemas no local.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, pode encaminhar o tráfego para uma VNet com a Tabela de Rotas do Hub Virtual.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?
 
Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como posso calcular o preço de um hub?
 
Pagaria para o serviço no hub. Por exemplo, 10 ramificações ou dispositivos no local que seja necessário para ligar a WAN Virtual do Azure implicariam ligar pontos finais VPN no hub. Permite que dizer que esta é a VPN de unidade de escala de 1 = 500 Mbps, isso é cobrado em US $0.361/ h. Cada ligação é cobrada em US $ 0,08/h. Para 10 conexões, o encargo total de serviço/h seria US $0.361 + $. 8 / RH. Encargos de dados de deixar o tráfego do azure aplicam-se. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Enviar um e-mail para azurevirtualwan@microsoft.com. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se estou usando um dispositivo i não está na lista de parceiros de Virtual WAN? Posso continuar a utilizá-lo para ligar a VPN de WAN Virtual do Azure?

Sim, desde que o dispositivo suporta IPsec IKEv1 ou IKEv2. Parceiros WAN virtual automatizam a conectividade do dispositivo para pontos de extremidade de VPN do Azure. Isso implica automatizando passos, como "carregamento de informações do ramo", "IPsec e configuração" e 'conectividade'. Uma vez que o seu dispositivo não é de um ecossistema de parceiros Virtual WAN, terá de fazer o trabalho pesado de colocar a configuração do Azure e atualizar o seu dispositivo para configurar a conectividade IPsec manualmente. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN Virtual com um hub e um vpnsite podem ser criada com uma [início rápido do Azure modelo](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN Virtual é principalmente um serviço orientado pelo REST ou pelo Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute. Embora o VPN site a site esteja em GA, o ExpressRoute e o ponto a site estão atualmente em Pré-visualização.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de Ramificação a Ramificação atravessa a WAN Virtual do Azure?

Sim.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Como é que a WAN Virtual é diferente do Gateway de Rede Virtual do Azure?

O VPN de Gateway de Rede Virtual está limitado a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Pode ligar-se até 1000 ligações de ramo com 2 Gbps no hub em todas as regiões, exceto na região Centro-Oeste. Para a região Centro-Oeste, estão disponíveis 20 Gbps. Estarão disponíveis 20 Gbps para mais regiões no futuro. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Pode ter um hub por região, o que significa que se pode ligar a mais de 1000 ramos nos hubs.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como é Virtual WAN que suporta dispositivos SD-WAN?

Parceiros WAN virtual automatizam a conectividade IPsec com pontos de extremidade de VPN do Azure. Se o parceiro de Virtual WAN for um fornecedor de SD-WAN, é implícita que o controlador de SD-WAN gerencia a automatização e a conectividade IPsec com pontos de extremidade de VPN do Azure. Se o dispositivo de SD-WAN requer seu próprio ponto final em vez de VPN do Azure para qualquer funcionalidade SD-WAN proprietária, pode implementar o ponto de final de SD-WAN numa VNet do Azure e coexistir com WAN Virtual do Azure.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Esta WAN Virtual precisa do ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Utiliza a conectividade de site a site standard IPsec através de ligações da Internet a partir do dispositivo para um hub de WAN Virtual do Azure. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para Sites que estão ligados através do ExpressRoute no Hub Virtual (Sob a Pré-visualização), os sites podem ter fluxo de tráfego de ramo para ramo entre o VPN e o ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de ramos está limitado a 1000 ligações por hub/região e um total de 2 Gbps no hub. A exceção é a região EUA Centro-Oeste, que tem um total de 20 Gbps. Vamos implementar 20 Gbps para outras regiões no futuro.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo no local utilize vários ISP em paralelo ou trata-se sempre de um único túnel VPN?

Uma ligação para Virtual WAN VPN é sempre um túnel de ativo-ativo (para resiliência dentro da mesma hub/região) através de uma ligação disponível na filial. Esta ligação pode ser uma ligação de ISP no ramo no local. Virtual WAN não fornece qualquer lógica especial para configurar várias ISP em paralelo; gestão de ativação pós-falha em ISP no ramo completamente é uma operação de rede centrada no ramo. Pode utilizar a sua solução de SD-WAN favorita para fazer a seleção de caminho com o ramo.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: o dispositivo de ramificação -> ISP -> Microsoft edge -> controlador de domínio do Microsoft (VNet do hub) -> Microsoft edge -> ISP -> dispositivos ramo

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma ligação à Internet e o dispositivo físico que suporte a IPsec, preferencialmente de nossa integrada [parceiros](https://go.microsoft.com/fwlink/p/?linkid=2019615). Opcionalmente, pode gerir manualmente a configuração e a conectividade ao Azure a partir do seu dispositivo preferido.