---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c46c11ead645b93d7710d1e11636037e4dcaf8e7
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444581"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um vpngateway de WAN Virtual do Azure?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. Atualmente, o ExpressRoute para conectividade de WAN virtual está em versão prévia. Os dispositivos de ramificação CPE autoprovisionam e se conectam à WAN virtual do Azure. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [lista de parceiros preferenciais](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma conexão de ramificação para a WAN virtual do Azure?

Uma conexão de um dispositivo de ramificação na WAN virtual do Azure, composta de dois túneis IPsec ativos/ativos.

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

Há suporte para até 1.000 conexões por Hub virtual. Cada ligação consiste de dois túneis que estão numa configuração ativo-ativo. Os túneis terminam num vpngateway de Hub Virtual do Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Sim. O fluxo de tráfego ao iniciar seria do dispositivo local para o Microsoft edge mais próximo e depois para o Hub Virtual.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O peering de VNet Global é suportado com a WAN Virtual do Azure? 

 Não.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>As VNets spoke ligadas a um hub virtual podem comunicar entre si?

Sim. O spoke VNets pode se comunicar diretamente por meio do emparelhamento de rede virtual. No entanto, não damos suporte à comunicação de VNets transitivamente por meio do Hub. Para obter mais informações, veja [Peering de Rede Virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure. Em primeiro lugar, ligue a VNet da aplicação de rede virtual ao hub com uma ligação de Rede Virtual do Hub. Em seguida, crie uma rota do Hub Virtual com o salto seguinte a apontar para a Aplicação Virtual. Pode aplicar várias rotas à Tabela de Rotas do Hub Virtual. Quaisquer spokes ligados à VNet NVA têm de estar ligados adicionalmente ao hub virtual para garantir que as rotas de VNet do spoke são propagadas para sistemas no local.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Uma VNet de NVA pode ter um gateway de rede virtual?

Não. A VNet de NVA não pode ter um gateway de rede virtual se estiver ligada ao hub virtual. 

### <a name="is-there-support-for-bgp"></a>Existe suporte para BGP?

Sim, o BGP é suportado. Ao criar um site VPN, você pode fornecer os parâmetros BGP nele. Isso significa que todas as conexões criadas no Azure para esse site serão habilitadas para BGP. Além disso, se você tivesse uma VNet com um NVA, e se essa VNet NVA fosse anexada a um hub de WAN virtual, para garantir que as rotas de uma VNet NVA sejam anunciadas adequadamente, os spokes anexados à VNet NVA devem desabilitar o BGP. Além disso, conecte essas VNets de spoke à VNet do Hub virtual para garantir que as rotas de VNet do spoke sejam propagadas para sistemas locais.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, pode encaminhar o tráfego para uma VNet com a Tabela de Rotas do Hub Virtual.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?
 
Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como fazer calcular o preço de um hub?
 
Você pagaria pelo serviço no Hub. Por exemplo, 10 branches ou dispositivos locais que exigem a conexão com a WAN virtual do Azure implicariam se conectar a pontos de extremidade de VPN no Hub. Digamos que esta seja a VPN de 1 unidade de escala = 500 Mbps, que é cobrada a $0.361/hr. Cada conexão é cobrada a $% 0,08/hr. Para 10 conexões, a taxa total de serviço/h seria $0.361 + $. 8/ Human. Encargos de dados de tráfego que saem do Azure se aplicam. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Enviar um e-mail para azurevirtualwan@microsoft.com. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou usando não estiver na lista de parceiros de WAN virtual? Ainda posso usá-lo para se conectar à VPN de WAN virtual do Azure?

Sim, contanto que o dispositivo dê suporte a IPsec IKEv1 ou IKEv2. Parceiros de WAN virtual automatizam a conectividade do dispositivo para pontos de extremidade de VPN do Azure. Isso implica automatizar etapas como ' upload de informações de ramificação ', ' IPsec e configuração ' e ' conectividade '. Como seu dispositivo não é de um ecossistema de parceiros de WAN virtual, você precisará fazer o trabalho pesado de usar manualmente a configuração do Azure e atualizar seu dispositivo para configurar a conectividade IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN virtual com um Hub e uma vpnsite pode ser criada usando um [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN Virtual é principalmente um serviço orientado pelo REST ou pelo Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute. Embora a VPN site a site esteja disponível, o ExpressRoute está atualmente em versão prévia.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de Ramificação a Ramificação atravessa a WAN Virtual do Azure?

Sim.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Como é que a WAN Virtual é diferente do Gateway de Rede Virtual do Azure?

O VPN de Gateway de Rede Virtual está limitado a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Você pode conectar até 1.000 conexões de ramificação com 20 Gbps no Hub para todas as regiões. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Você pode ter um hub por região, o que significa que você pode conectar mais de 1.000 ramificações entre hubs.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como a WAN virtual dá suporte a dispositivos SD-WAN?

Parceiros de WAN virtual automatizam a conectividade IPsec para pontos de extremidade de VPN do Azure. Se o parceiro WAN virtual for um provedor SD-WAN, ele estará implícito de que o controlador SD-WAN gerencia a automação e a conectividade IPsec para pontos de extremidade de VPN do Azure. Se o dispositivo SD-WAN exigir seu próprio ponto de extremidade em vez da VPN do Azure para qualquer funcionalidade SD-WAN proprietária, você poderá implantar o ponto de extremidade SD-WAN em uma VNet do Azure e coexistir com a WAN virtual do Azure.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Esta WAN Virtual precisa do ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Utiliza a conectividade de site a site standard IPsec através de ligações da Internet a partir do dispositivo para um hub de WAN Virtual do Azure. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para Sites que estão ligados através do ExpressRoute no Hub Virtual (Sob a Pré-visualização), os sites podem ter fluxo de tráfego de ramo para ramo entre o VPN e o ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de ramificações é limitado a 1000 conexões por Hub/região e um total de 20 Gbps no Hub.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>A quantas conexões VPN um hub de WAN virtual dá suporte?

Um hub de WAN virtual do Azure pode dar suporte a até 1.000 conexões S2S e 10.000 conexões P2Ss simultaneamente.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual é a taxa de transferência de VPN total de um túnel VPN e uma conexão?

A taxa de transferência de VPN total de um hub é de até 20 Gbps com base na unidade de escala escolhida. A taxa de transferência é compartilhada por todas as conexões existentes.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo no local utilize vários ISP em paralelo ou trata-se sempre de um único túnel VPN?

Uma conexão entrando na VPN de WAN virtual é sempre um túnel ativo-ativo (para resiliência dentro do mesmo Hub/região) usando um link disponível na ramificação. Esse link pode ser um link de ISP na ramificação local. A WAN virtual não fornece nenhuma lógica especial para configurar vários ISPs em paralelo; o gerenciamento de failover entre o ISP na ramificação é completamente uma operação de rede centrada em ramificação. Você pode usar sua solução de SD-WAN favorita para fazer a seleção de caminho na ramificação.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramificação-> ISP-> Microsoft Edge-> Microsoft DC (VNet do Hub)-> Microsoft Edge-> ISP – > dispositivo de ramificação

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma conexão com a Internet e um dispositivo físico que oferece suporte a IPsec, preferencialmente de nossos [parceiros](https://go.microsoft.com/fwlink/p/?linkid=2019615)integrados. Opcionalmente, pode gerir manualmente a configuração e a conectividade ao Azure a partir do seu dispositivo preferido.
