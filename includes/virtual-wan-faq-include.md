---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b65cf26bcea628f784eb086d1b9c88febade25f6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829028"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de VPN (gateway de rede virtual) do Azure e um gateway de VPN de WAN virtual do Azure?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. Quando você conecta um site a um gateway de VPN de WAN virtual, ele é diferente de um gateway de rede virtual regular que usa um tipo de gateway ' VPN '. Da mesma forma, quando você conecta um circuito de ExpressRoute a um hub de WAN virtual, ele usa um recurso diferente para o gateway de ExpressRoute do que o gateway de rede virtual regular que usa o tipo de gateway ' ExpressRoute '. A WAN virtual dá suporte à taxa de transferência de até 20 Gbps de agregação para VPN e ExpressRoute. A WAN virtual também tem automação para conectividade com um ecossistema de parceiros de dispositivo de ramificação CPE. Os dispositivos da ramificação CPE têm automação interna que provisiona automaticamente e se conecta à WAN virtual do Azure. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [lista de parceiros preferenciais](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como a WAN virtual é diferente de um gateway de rede virtual do Azure?

Uma VPN de gateway de rede virtual é limitada a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Você pode conectar até 1.000 conexões de ramificação por região (Hub virtual) com agregação de 20 Gbps por Hub. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Você pode ter um hub por região, o que significa que você pode conectar mais de 1.000 ramificações entre hubs.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais provedores de dispositivo (parceiros de WAN virtual) têm suporte?

Atualmente, muitos parceiros suportam a experiência de WAN Virtual totalmente automatizada. Para obter mais informações, veja [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são os passos de automatização dos parceiros da WAN Virtual?

Para obter os passos de automatização, veja [Automatização dos parceiros da WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como é que os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

As soluções de conectividade definida pelo software gerem, normalmente, os respetivos dispositivos de ramo com um controlador ou um centro de aprovisionamento de dispositivos. O controlador pode utilizar APIs do Azure para automatizar a conectividade à WAN Virtual do Azure. A automação inclui o carregamento de informações de ramificação, o download da configuração do Azure, a configuração de túneis IPSec em hubs virtuais do Azure e a configuração automática do formulário de conectividade do dispositivo de ramificação para a WAN virtual do Azure. Quando você tem centenas de ramificações, é fácil conectar-se usando parceiros CPE da WAN virtual, pois a experiência de integração elimina a necessidade de configurar, configurar e gerenciar a conectividade IPsec em larga escala. Para mais informações, veja [Automatização de parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como a WAN virtual dá suporte a dispositivos SD-WAN?

Parceiros de WAN virtual automatizam a conectividade IPsec para pontos de extremidade de VPN do Azure. Se o parceiro WAN virtual for um provedor SD-WAN, ele estará implícito de que o controlador SD-WAN gerencia a automação e a conectividade IPsec para pontos de extremidade de VPN do Azure. Se o dispositivo SD-WAN exigir seu próprio ponto de extremidade em vez da VPN do Azure para qualquer funcionalidade SD-WAN proprietária, você poderá implantar o ponto de extremidade SD-WAN em uma VNet do Azure e coexistir com a WAN virtual do Azure.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera funcionalidades de conectividade existentes?

As funcionalidades de conectividade do Azure existentes não sofrem quaisquer alterações.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existem novos recursos do Resource Manager disponíveis para a WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Resource Manager. Para obter mais informações, veja a [Descrição Geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem se conectar a um único Hub?

Há suporte para até 1.000 conexões por Hub virtual. Cada conexão consiste em quatro links e cada conexão de link dá suporte a dois túneis que estão em uma configuração ativa-ativa. Os túneis terminam em um vpngateway de Hub virtual do Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Sim. O fluxo de tráfego, ao começar, é do dispositivo local para a borda de rede da Microsoft mais próxima e, em seguida, para o Hub virtual.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure. Em primeiro lugar, ligue a VNet da aplicação de rede virtual ao hub com uma ligação de Rede Virtual do Hub. Em seguida, crie uma rota de Hub virtual com um próximo salto apontando para o dispositivo virtual. Você pode aplicar várias rotas à tabela de rotas do Hub virtual. Quaisquer spokes ligados à VNet NVA têm de estar ligados adicionalmente ao hub virtual para garantir que as rotas de VNet do spoke são propagadas para sistemas no local.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar uma solução de virtualização de rede dentro do Hub virtual?

Uma NVA (solução de virtualização de rede) não pode ser implantada dentro de um hub virtual. No entanto, você pode criá-lo em uma VNet spoke que está conectada ao Hub virtual e habilitar uma rota no Hub para direcionar o tráfego para VNet de destino por meio do endereço IP NVA (da NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Uma VNet spoke pode ter um gateway de rede virtual?

Não. A VNet do spoke não poderá ter um gateway de rede virtual se estiver conectada ao Hub virtual.

### <a name="is-there-support-for-bgp"></a>Existe suporte para BGP?

Sim, o BGP é suportado. Ao criar um site VPN, você pode fornecer os parâmetros BGP nele. Isso significa que todas as conexões criadas no Azure para esse site serão habilitadas para BGP. Além disso, se você tivesse uma VNet com um NVA, e se essa VNet NVA fosse anexada a um hub de WAN virtual, para garantir que as rotas de uma VNet NVA sejam anunciadas adequadamente, os spokes anexados à VNet NVA devem desabilitar o BGP. Além disso, conecte essas VNets de spoke à VNet do Hub virtual para garantir que as rotas de VNet do spoke sejam propagadas para sistemas locais.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, você pode direcionar o tráfego para uma VNet usando uma tabela de rotas do Hub virtual. Isso permite que você defina rotas para VNets de destino no Azure por meio de um endereço IP específico (normalmente da NIC NVA).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?

Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como fazer calcular o preço de um hub?

* Você pagaria pelos serviços no Hub. Por exemplo, digamos que você tenha 10 branches ou dispositivos locais que exigem a conexão com a WAN virtual do Azure significariam conectar-se a pontos de extremidade de VPN no Hub. Digamos que esta seja a VPN de 1 unidade de escala = 500 Mbps, que é cobrada a $0.361/hr. Cada conexão é cobrada a US $0,05/hr. Para 10 conexões, a taxa total de serviço/h seria $0.361 + $. 5/hr. Encargos de dados de tráfego que saem do Azure se aplicam.

* Há cobranças de Hub adicionais. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Se você tivesse o gateway de ExpressRoute devido a circuitos do ExpressRoute se conectando a um hub virtual, você pagaria pelo preço unitário da escala. Cada unidade de escala em ER é de 2 Gbps e cada unidade de conexão é cobrada com a mesma taxa que a unidade de conexão VPN.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Todas as APIs de WAN virtual são API aberta. Você pode passar pela documentação para avaliar a viabilidade técnica. Se você tiver alguma pergunta, envie um email para azurevirtualwan@microsoft.com. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou usando não estiver na lista de parceiros de WAN virtual? Ainda posso usá-lo para se conectar à VPN de WAN virtual do Azure?

Sim, contanto que o dispositivo dê suporte a IPsec IKEv1 ou IKEv2. Parceiros de WAN virtual automatizam a conectividade do dispositivo para pontos de extremidade de VPN do Azure. Isso implica automatizar etapas como ' upload de informações de ramificação ', ' IPsec e configuração ' e ' conectividade '. Como seu dispositivo não é de um ecossistema de parceiros de WAN virtual, você precisará fazer o trabalho pesado de usar manualmente a configuração do Azure e atualizar seu dispositivo para configurar a conectividade IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN virtual com um Hub e uma vpnsite pode ser criada usando um [modelo de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). A WAN virtual é principalmente um serviço direcionado a um REST ou Portal.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O peering de VNet Global é suportado com a WAN Virtual do Azure? 

Você pode conectar uma VNet em uma região diferente da WAN virtual.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>As VNets spoke ligadas a um hub virtual podem comunicar entre si?

Sim. A WAN virtual padrão dá suporte à conectividade transitiva vnet para vnet por meio do Hub WAN virtual ao qual os Vnets estão conectados. Na terminologia da WAN virtual, nos referimos a esses caminhos como "trânsito da VNet da WAN virtual local" para VNets conectados a um hub de WAN virtual dentro de uma única região e "trânsito da VNet global de WAN virtual" para VNets conectados por meio de vários hubs de WAN virtual em dois ou mais regiões. O trânsito de VNet dá suporte a até 3 Gbps de taxa de transferência durante a visualização pública. A taxa de transferência será expandida quando o trânsito global ficar GA.   

Para alguns cenários, o spoke Vnets também pode ser diretamente emparelhado entre si usando o [emparelhamento de rede virtual](../articles/virtual-network/virtual-network-peering-overview.md) , além do trânsito local ou global da VNET de WAN virtual. Nesse caso, o emparelhamento vnet tem precedência sobre a conexão transitiva por meio do Hub WAN virtual. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma conexão de ramificação para a WAN virtual do Azure?

Uma conexão de um dispositivo de ramificação na WAN virtual do Azure dá suporte a até quatro links. Um link é o link de conectividade física no local da ramificação (por exemplo: ATT, Verizon etc.). Cada conexão de link é composta por dois túneis IPsec ativos/ativos.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de ramificação para ramificação atravessa a WAN virtual do Azure?

Sim.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>A WAN virtual requer o ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Ele usa a conectividade padrão de site a site do IPsec por meio de links da Internet do dispositivo para um hub de WAN virtual do Azure. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para sites conectados usando o ExpressRoute em um hub virtual, os sites podem ter o fluxo de tráfego de ramificação para ramificação entre VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de ramificações é limitado a 1000 conexões por Hub/região e um total de 20 Gbps no Hub. Você pode ter 1 hub por região.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>A quantas conexões VPN um hub de WAN virtual dá suporte?

Um hub de WAN virtual do Azure pode dar suporte a até 1.000 conexões S2S, 10.000 conexões P2S e 4 conexões de ExpressRoute simultaneamente.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual é a taxa de transferência de VPN total de um túnel VPN e uma conexão?

A taxa de transferência de VPN total de um hub é de até 20 Gbps com base na unidade de escala escolhida. A taxa de transferência é compartilhada por todas as conexões existentes. Cada túnel em uma conexão pode dar suporte a até 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Não vejo a configuração de 20 Gbps para o Hub virtual no Portal. Como fazer configurar isso?

Navegue até o gateway de VPN dentro de um Hub no portal e clique na unidade de escala para alterá-la para a configuração apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN virtual permite que o dispositivo local utilize vários ISPs em paralelo, ou sempre é um único túnel de VPN?

Uma conexão que entra em uma VPN de WAN virtual é sempre um túnel ativo-ativo (para resiliência dentro do mesmo Hub/região) usando um link disponível na ramificação. Esse link pode ser um link de ISP na ramificação local. A WAN virtual ' VPNSite ' fornece a capacidade de adicionar informações de link ao site. Se você tiver vários ISPs na ramificação e cada um dos ISPs forneceu um link, essas informações poderão ser configuradas nas informações do site de VPN no Azure. No entanto, o gerenciamento de failover entre ISPs na ramificação é completamente uma operação de roteamento centrada em ramificações.

### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura de trânsito global, consulte [arquitetura de rede de trânsito global e WAN virtual](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramificação-> ISP-> Microsoft Network Edge-> Microsoft DC (Hub VNet)-> Microsoft Network Edge-> ISP-> o dispositivo de ramificação

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma conexão com a Internet e um dispositivo físico que dá suporte a IPsec, preferencialmente de nossos [parceiros de WAN virtuais](../articles/virtual-wan/virtual-wan-locations-partners.md)integrados. Opcionalmente, você pode gerenciar manualmente a configuração e a conectividade com o Azure do seu dispositivo preferencial.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como fazer habilitar a rota padrão (0.0.0.0/0) em uma conexão (VPN, ExpressRoute ou rede virtual):

Um hub virtual pode propagar uma rota padrão aprendida para uma conexão VPN/ExpressRoute de rede virtual/site a site se o sinalizador for ' habilitado ' na conexão. Esse sinalizador fica visível quando o usuário edita uma conexão de rede virtual, uma conexão VPN ou uma conexão de ExpressRoute. Por padrão, esse sinalizador é desabilitado quando um site ou um circuito do ExpressRoute é conectado a um Hub. Ele é habilitado por padrão quando uma conexão de rede virtual é adicionada para conectar uma VNet a um hub virtual. A rota padrão não é originada no Hub WAN virtual; a rota padrão será propagada se já tiver sido aprendida pelo Hub WAN virtual como resultado da implantação de um firewall no Hub ou se outro site conectado tiver o túnel forçado habilitado.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN virtuais (básico e Standard)?

O tipo de WAN "básico" permite criar um hub básico (SKU = básico). Um tipo de WAN "Standard" permite criar um hub padrão (SKU = Standard). Os hubs básicos são limitados à funcionalidade de VPN site a site. Os hubs padrão permitem que você tenha o ExpressRoute, a VPN de usuário (P2S), o Hub de malha completa e o trânsito de VNet a VNet por meio dos hubs. Você paga um encargo base de $0,25/h para os hubs padrão e uma taxa de processamento de dados para o tráfego por meio dos hubs durante a conectividade de VNet para VNet, bem como o processamento de dados para o transporte de Hub ao Hub. Para obter mais informações, consulte as [WANs virtuais básica e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Para obter os preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/virtual-wan/) .
