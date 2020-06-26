---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7144cb18d19fd6be040b0a6ca4e8bb18498dfe6c
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85365201"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O utilizador precisa de ter hub e falou com dispositivos SD-WAN/VPN para utilizar o Azure Virtual WAN?

O WAN virtual fornece muitas funcionalidades incorporadas num único painel de vidro, tais como conectividade VPN site/site-to-site, conectividade user/P2S, conectividade ExpressRoute, conectividade virtual, interconectividade VPN ExpressRoute, VNET para conectividade transitiva VNET, Encaminhamento Centralizado, firewall Azure e segurança do gestor de firewall, Monitorização, Encriptação ExpressRoute e muitas outras capacidades. Não é preciso ter todos estes casos de utilização para começar a usar WAN Virtual. Pode simplesmente começar com apenas um caso de uso. A arquitetura Virtual WAN é um hub e arquitetura falada com escala e performance incorporada onde os balcões (dispositivos VPN/SD-WAN), utilizadores (Clientes Azure VPN, clientes openVPN ou IKEv2), circuitos ExpressRoute, Redes Virtuais servem como porta-vozes para o Virtual Hub(s). Todos os hubs estão ligados em malha completa num WAN Virtual Standard, facilitando o uso da espinha dorsal da Microsoft para qualquer conectividade (qualquer um falado). Para o hub e falou com dispositivos SD-WAN/VPN, os utilizadores podem instalá-lo manualmente no portal Azure Virtual WAN ou utilizar o Parceiro Virtual WAN CPE (SD-WAN/VPN) para configurar a conectividade com o Azure. Os parceiros VIRTUAIS WAN fornecem automatização para conectividade que é a capacidade de exportar a informação do dispositivo para o Azure, baixar a configuração Azure e estabelecer conectividade com o hub Azure Virtual WAN. Para a conectividade Point-to-site/User VPN, apoiamos [o cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)o cliente OpenVPN ou o cliente IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Que cliente suporta o suporte do utilizador Azure Virtual WAN VPN (Ponto a local)?

A WAN virtual suporta [o cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)o Cliente OpenVPN ou qualquer cliente IKEv2. A autenticação AZure AD é suportada com O Cliente Azure VPN.É necessário um mínimo da versão OS do cliente Windows 10 17763.0 ou superior.  Os clientes(s) OpenVPN podem suportar a autenticação baseada em certificados. Assim que o auth baseado em cert for selecionado no gateway, verá o ficheiro .ovpn para descarregar no seu dispositivo. Tanto o certificado como o radius auth são suportados com iKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para a VPN do utilizador (ponto a local)- Porque é que a piscina de clientes P2S está dividida em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece para que cada instância de gateway possa alocar iPs de clientes independentemente para clientes conectados e o tráfego da rede virtual é encaminhado de volta para a instância de gateway correta para evitar o lúpulo de instância inter-gateway.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como adiciono servidores DNS para clientes P2S?

Existem duas opções para adicionar servidores DNS para os clientes P2S. O primeiro método é preferido, uma vez que adiciona os servidores DNS personalizados ao gateway em vez do cliente.

1. Utilize o seguinte script powershell para adicionar os servidores DNS personalizados. Por favor, substitua os valores para o seu ambiente.
```
// Define variables
$rgName = "testRG1"
$virtualHubName = "virtualHub1"
$P2SvpnGatewayName = "testP2SVpnGateway1"
$vpnClientAddressSpaces = 
$vpnServerConfiguration1Name = "vpnServerConfig1"
$vpnClientAddressSpaces = New-Object string[] 2
$vpnClientAddressSpaces[0] = "192.168.2.0/24"
$vpnClientAddressSpaces[1] = "192.168.3.0/24"
$customDnsServers = New-Object string[] 2
$customDnsServers[0] = "7.7.7.7"
$customDnsServers[1] = "8.8.8.8"
$virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
$vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

// Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

// Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
$P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
$updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

// Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
```
2. Ou, se estiver a utilizar o Cliente VPN Azure para o Windows 10, ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** pode modificar o perfil descarregado do ficheiro XML e adicionar as etiquetas antes de o importar.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Para a VPN do utilizador (ponto a local)- quantos clientes são suportados?

Cada gateway P2S do Utilizador tem duas instâncias e cada instância suporta certos utilizadores à medida que a unidade de escala muda. A unidade de escala 1-3 suporta 500 ligações, a unidade de escala 4-6 suporta 1000 ligações, a unidade de escala 7-12 suporta 5000 ligações e a unidade de escala 13-20 suporta até 10.000 ligações. Como exemplo, digamos que o utilizador escolhe uma unidade de escala de 1. Cada unidade de escala implicaria um gateway ativo implantado e cada uma das instâncias (neste caso 2) suportaria até 500 ligações. Uma vez que pode obter 500 ligações * 2 por gateway, não significa que planeie 1000 em vez dos 500 para esta unidade de escala, pois os casos podem ter de ser reparados durante os quais a conectividade para os 500 extras pode ser interrompida se ultrapassar a contagem de ligação recomendada. Além disso, certifique-se de planear o tempo de inatividade no caso de decidir escalar para cima ou para baixo na unidade de escala ou alterar a configuração ponto-a-local no gateway VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual Azure (VPN Gateway) e um gateway Azure Virtual WAN VPN?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. Quando liga um site a um gateway Virtual WAN VPN, é diferente de um gateway de rede virtual regular que utiliza um tipo de gateway 'VPN'. Da mesma forma, quando liga um circuito ExpressRoute a um hub Virtual WAN, utiliza um recurso diferente para o gateway ExpressRoute do que o gateway de rede virtual regular que utiliza o tipo de gateway 'ExpressRoute'. O WAN virtual suporta até 20 Gbps de produção agregada tanto para VPN como ExpressRoute. A WAN virtual também tem automatização para conectividade com um ecossistema de parceiros de dispositivos de filial CPE. Os dispositivos de filial CPE têm automatização incorporada que fornece automaticamente e conecta-se a Azure Virtual WAN. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [Lista de Parceiros Preferidos.](../articles/virtual-wan/virtual-wan-locations-partners.md)

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como é que o VIRTUAL WAN é diferente de um gateway de rede virtual Azure?

Uma VPN de gateway de rede virtual está limitada a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Pode ligar até 1.000 ligações de ramificação por região (hub virtual) com um agregado de 20 Gbps por hub. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Você pode ter um hub por região, o que significa que você pode conectar mais de 1.000 ramos através de centros.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma Unidade de Escala de Gateway VIRTUAL WAN
Uma unidade de escala é uma unidade definida para escolher uma produção agregada de um gateway no centro virtual. 1 unidade de escala de VPN = 500 Mbps . 1 unidade de escala de ExpressRoute = 2 Gbps. Exemplo: 10 unidades de VPN à escala implicariam 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais os fornecedores de dispositivos (parceiros VIRTUAIS WAN) são suportados?

Atualmente, muitos parceiros suportam a experiência de WAN Virtual totalmente automatizada. Para obter mais informações, veja [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são os passos de automatização dos parceiros da WAN Virtual?

Para obter os passos de automatização, veja [Automatização dos parceiros da WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1. A VIRTUAL WAN também tem soluções parceiras CPE que automatizam a conectividade com a Azure Virtual WAN, facilitando a configuração de ligações VPN IPsec em escala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como é que os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

As soluções de conectividade definida pelo software gerem, normalmente, os respetivos dispositivos de ramo com um controlador ou um centro de aprovisionamento de dispositivos. O controlador pode utilizar APIs do Azure para automatizar a conectividade à WAN Virtual do Azure. A automatização inclui o upload de informações do ramo, o descarregamento da configuração Azure, a criação de túneis IPSec em centros virtuais Azure e a configuração automática da conectividade do dispositivo de ramo para Azure Virtual WAN. Quando se tem centenas de agências, a ligação utilizando parceiros VIRTUAIS WAN CPE é fácil porque a experiência de embarque retira a necessidade de configurar, configurar e gerir a conectividade IPsec em larga escala. Para obter mais informações, consulte [a automatização de parceiros Virtual WAN.](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou a usar não estiver na lista de parceiros do Wan Virtual? Ainda posso usá-lo para ligar ao Azure Virtual WAN VPN?

Sim, desde que o dispositivo suporte iPsec IKEv1 ou IKEv2. Os parceiros WAN virtuais automatizam a conectividade do dispositivo para os pontos finais Azure VPN. Isto implica automatizar etapas como 'branch information upload', 'IPsec e configuração' e 'conectividade'. Uma vez que o seu dispositivo não é de um ecossistema parceiro Virtual WAN, terá de fazer o levantamento pesado de tomar manualmente a configuração Azure e atualizar o seu dispositivo para configurar a conectividade IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Todas as APIs wan virtuais estão abertas API. Pode ver a documentação [A automatização do parceiro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) para avaliar a viabilidade técnica. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec. Uma vez concluída a empresa o trabalho de automatização do seu dispositivo CPE com base nas diretrizes de automatização acima indicadas, pode chegar azurevirtualwan@microsoft.com para ser listado aqui Conectividade através de [parceiros.]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners) Se é um cliente que gostaria que uma determinada solução da empresa fosse listada como um parceiro Virtual WAN, por favor contacte a empresa contacte a WAN Virtual enviando um e-mail para azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como é que o WAN virtual suporta dispositivos SD-WAN?

Os parceiros virtuais wan automatizam a conectividade IPsec para os pontos finais do Azure VPN. Se o parceiro Virtual WAN for um fornecedor SD-WAN, então está implícito que o controlador SD-WAN gere a automação e a conectividade IPsec com os pontos finais do Azure VPN. Se o dispositivo SD-WAN necessitar do seu próprio ponto final em vez de Azure VPN para qualquer funcionalidade proprietária da SD-WAN, pode implantar o ponto final SD-WAN num VNet Azure e coexistir com a Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera funcionalidades de conectividade existentes?

As funcionalidades de conectividade do Azure existentes não sofrem quaisquer alterações.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existem novos recursos do Resource Manager disponíveis para a WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Resource Manager. Para obter mais informações, veja a [Descrição Geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem ligar-se a um único hub?

Até 1.000 ligações são suportadas por hub virtual. Cada ligação é constituída por quatro ligações e cada ligação suporta dois túneis que se encontram numa configuração ativa. Os túneis terminam num centro virtual de Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Sim. O fluxo de tráfego, quando começa, é do dispositivo no local para a borda de rede mais próxima da Microsoft e, em seguida, para o centro virtual.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure. Em primeiro lugar, ligue a VNet da aplicação de rede virtual ao hub com uma ligação de Rede Virtual do Hub. Em seguida, crie uma rota de hub virtual com um próximo lúpulo apontando para o Aparelho Virtual. Pode aplicar várias rotas para a tabela de rotas do hub virtual. Quaisquer spokes ligados à VNet NVA têm de estar ligados adicionalmente ao hub virtual para garantir que as rotas de VNet do spoke são propagadas para sistemas no local.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar um Aparelho Virtual de Rede dentro do centro virtual?

Um aparelho virtual de rede (NVA) não pode ser implantado dentro de um centro virtual. No entanto, pode criá-lo num VNet falado que esteja ligado ao hub virtual e permitir uma rota no centro para direcionar o tráfego para o destino VNet através do endereço IP NVA (do NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Um VNet falado pode ter uma porta de entrada de rede virtual?

Não. O VNet falado não pode ter uma porta de entrada de rede virtual se estiver ligado ao centro virtual.

### <a name="is-there-support-for-bgp"></a>Existe suporte para BGP?

Sim, o BGP é suportado. Quando criar um site VPN, pode fornecer os parâmetros BGP nele. Isto implicará que quaisquer ligações criadas em Azure para esse site serão ativadas para bGP. Além disso, se tivesse um VNet com um NVA, e se este VNet NVA estivesse ligado a um hub De WAN Virtual, de modo a garantir que as rotas de um VNet NVA são publicitadas adequadamente, os raios que estão ligados ao NVA VNet devem desativar o BGP. Além disso, ligue estes VNets de raios ao hub virtual VNet para garantir que as rotas VNet faladas são propagadas aos sistemas no local.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, você pode direcionar o tráfego para um VNet usando uma tabela de rota de hub virtual. Isto permite-lhe definir rotas para VNets de destino em Azure através de um endereço IP específico (normalmente do NVA NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?

Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como calculo o preço de um centro?

* Pagaria pelos serviços no centro. Por exemplo, digamos que tem 10 agências ou dispositivos no local que necessitam de se ligar ao Azure Virtual WAN implicaria a ligação aos pontos finais VPN no centro. Digamos que esta é VPN de 1 unidade de escala = 500 Mbps, isto é cobrado a $0.361/hora. Cada ligação é cobrada a $0,05/hora. Para 10 ligações, o custo total do serviço/hora seria $0.361 + $.5/hr. Aplicam-se os encargos com o tráfego que sai do Azure.

* Há uma carga adicional do centro. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Se tivesse o gateway ExpressRoute devido aos circuitos ExpressRoute que se ligavam a um hub virtual, então pagaria pelo preço unitário da escala. Cada unidade de escala em ER é de 2 Gbps e cada unidade de ligação é carregada à mesma velocidade que a unidade de ligação VPN.

* Se tivesses falado VNETs ligados ao centro, as acusações de espreitar os VNETs falados ainda se aplicam. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de um WAN Virtual com um hub e um vpnsite pode ser criada usando um [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN virtual é principalmente um serviço DE REST ou portal.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O peering de VNet Global é suportado com a WAN Virtual do Azure? 

Pode ligar um VNet numa região diferente da sua WAN virtual.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Os VNets falados ligados a um hub virtual podem comunicar entre si (V2V Transit)?

Sim. O STANDARD Virtual WAN suporta a conectividade transitiva Vnet através do hub VIRTUAL WAN a que os Vnets estão ligados. Na terminologia virtual WAN, referimo-nos a estes caminhos como "trânsito virtual local de WAN VNet" para VNets ligados a um Centro De Wan Virtual dentro de uma única região, e "global de trânsito virtual WAN VNet" para VNets conectados através de múltiplos Hubs VIRTUAIS WAN em duas ou mais regiões. O trânsito VNet suporta até 3 Gbps de produção durante a pré-visualização pública. A produção expandir-se-á quando o trânsito global for ga.

NOTA: Atualmente, a pré-visualização do trânsito V2V requer que seja implantado um GW VPN num Hub Virtual para acionar os elementos de encaminhamento a serem lançados. Este VPN GW não é utilizado para o caminho de trânsito V2V. Esta é uma limitação conhecida e será removida no momento do V2V GA. Pode eliminar o Gateway VPN no(s) hub(s) depois de ser totalmente lançado, uma vez que não é necessário para a funcionalidade de trânsito V2V. 

Para alguns cenários, os Vnets falados também podem ser diretamente espreitados uns com os outros usando [o Virtual Network Peering,](../articles/virtual-network/virtual-network-peering-overview.md) além do trânsito virtual WAN VNet local ou global. Neste caso, a Vnet Peering tem precedência sobre a ligação transitiva através do hub VIRTUAL WAN. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma ligação de ramo ao Azure Virtual WAN?

Uma ligação de um dispositivo de ramificação para Azure Virtual WAN suporta até quatro ligações. Uma ligação é a ligação de conectividade física no local do ramo (por exemplo: ATT, Verizon etc.). Cada ligação de ligação é composta por dois túneis IPsec ativos/ativos.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de ramo-a-ramo atravessa o Azure Virtual WAN?

Sim.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>O WAN virtual requer o ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Utiliza a conectividade local-local padrão do IPsec através de ligações à Internet do dispositivo para um hub Azure Virtual WAN. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para sites que estão conectados usando ExpressRoute em um hub virtual, os sites podem ter ramo para ramificar o fluxo de tráfego entre VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de ramos é limitado a 1000 ligações por hub/região e um total de 20 Gbps no centro. Pode ter 1 hub por região.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Quantas ligações VPN suporta um hub virtual WAN?

Um hub Azure Virtual WAN pode suportar até 1.000 ligações S2S, 10.000 ligações P2S e 4 ligações ExpressRoute simultaneamente.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual é a produção total de VPN de um túnel VPN e uma ligação?

A produção total de VPN de um hub é de até 20 Gbps com base na unidade de escala escolhida. A produção é partilhada por todas as ligações existentes. Cada túnel numa ligação pode suportar até 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Não vejo a definição de 20 Gbps para o centro virtual no portal. Como posso configurar isto?

Navegue até ao gateway VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a definição apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>O VIRTUAL WAN permite que o dispositivo no local utilize vários ISPs em paralelo, ou é sempre um único túnel VPN?
As soluções de dispositivos no local podem aplicar políticas de tráfego para orientar o tráfego através de vários túneis para Azure.


### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura global de trânsito, consulte [a arquitetura da rede de trânsito Global e a Virtual WAN.](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramo ->ISP->Microsoft rede edge->Microsoft edge->Microsoft DC (hub VNet)->dispositivo >de >de rede da Microsoft

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma ligação à Internet e dispositivo físico que suporta o IPsec, de preferência dos nossos [parceiros integrados virtual WAN.](../articles/virtual-wan/virtual-wan-locations-partners.md) Opcionalmente, pode gerir manualmente a configuração e conectividade com o Azure a partir do seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como posso permitir a rota predefinidora (0.0.0.0/0) numa ligação (VPN, ExpressRoute ou Rede Virtual):

Um hub virtual pode propagar uma rota padrão aprendida para uma ligação VPN/ExpressRoute de rede virtual/local se a bandeira estiver 'Activada' na ligação. Esta bandeira é visível quando o utilizador edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. Por predefinição, esta bandeira é desativada quando um site ou um circuito ExpressRoute estão ligados a um hub. É ativado por padrão quando uma ligação de rede virtual é adicionada para ligar um VNet a um hub virtual. A rota predefinida não tem origem no hub Virtual WAN; a rota padrão é propagada se já for aprendida pelo hub Virtual WAN como resultado da implantação de uma firewall no centro, ou se outro site conectado tiver um túnel forçado habilitado.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Como é que o hub virtual num WAN virtual seleciona o melhor caminho para uma rota a partir de vários hubs

Se um Virtual Hub aprende a mesma rota a partir de vários centros remotos, a ordem pela qual decide é a seguinte
1. Jogo de prefixo mais longo
2. Rotas locais sobre o interhub
3. Rotas estáticas sobre bGP
4. ExpressRoute (ER) sobre VPN
5. Comprimento do caminho AS

O trânsito entre o ER e o ER é sempre via Alcance Global devido ao qual, se o pedido chegar através de ER num centro e houver uma VPN e ER num hub remoto, a VPN será preferível em vez de ER a partir de um hub remoto para chegar a um ponto final ligado via VPN ou ER no hub remoto


### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Existe suporte para o IPv6 em VIRTUAL WAN?

O IPv6 não é suportado no centro VIRTUAL WAN e nas suas portas de entrada. Se tem um VNET que tem suporte IPv6 e gostaria de ligar o VNET ao WAN Virtual, este cenário também não é suportado. 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN Virtual (Básico e Standard)?

O tipo WAN 'Básico' permite criar um hub básico (SKU = Básico). Um tipo WAN 'Standard' permite criar um hub padrão (SKU = Standard). Os centros básicos estão limitados à funcionalidade VPN site-to-site. Os hubs standard permitem-lhe ter ExpressRoute, User VPN (P2S), full mesh hub e VNet-to-VNet transitar através dos hubs. Você paga uma taxa base de $0,25/h para os hubs padrão e uma taxa de processamento de dados para transitar através dos hubs durante a conectividade VNet-to-VNet, bem como o processamento de dados para o tráfego do hub para o hub. Para obter mais informações, consulte [AS WANs virtuais Básicas e Standard.](../articles/virtual-wan/virtual-wan-about.md#basicstandard) Para obter preços, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/virtual-wan/)
