---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: af0082ef04416d6b3700bbcd96995a154614e0d6
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798555"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Azure Virtual WAN está na AG?

Sim, Azure Virtual WAN está geralmente disponível (GA). No entanto, o VIRTUAL WAN consiste em várias funcionalidades e cenários. Existem funcionalidades ou cenários dentro do VIRTUAL WAN onde a Microsoft aplica a etiqueta de pré-visualização. Nesses casos, a característica específica, ou o próprio cenário, está em Pré-Visualização. Se não utilizar uma função de pré-visualização específica, aplica-se um suporte regular para a AG. Para obter mais informações sobre o suporte de pré-visualização, consulte [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O utilizador precisa de ter hub e falou com dispositivos SD-WAN/VPN para utilizar o Azure Virtual WAN?

O WAN virtual fornece muitas funcionalidades incorporadas num único painel de vidro, tais como conectividade VPN site/site-to-site, conectividade user/P2S, conectividade ExpressRoute, conectividade virtual, interconectividade VPN ExpressRoute, conectividade transitiva VNet-to-VNet, Encaminhamento Centralizado, Firewall Azure e Segurança do Gestor de Firewall, Monitorização, Encriptação ExpressRoute, e muitas outras capacidades. Não é preciso ter todos estes casos de utilização para começar a usar WAN Virtual. Pode começar com apenas um caso de uso. A arquitetura Virtual WAN é um hub e arquitetura falada com escala e performance construídas onde os balcões (dispositivos VPN/SD-WAN), utilizadores (Clientes Azure VPN, openVPN, ou IKEv2 Clients), circuitos ExpressRoute, Redes Virtuais servem como porta-vozes para o Virtual Hub(s). Todos os hubs estão ligados em malha completa num WAN Virtual Standard, facilitando o uso da espinha dorsal da Microsoft para qualquer conectividade (qualquer um falado). Para o hub e falou com dispositivos SD-WAN/VPN, os utilizadores podem instalá-lo manualmente no portal Azure Virtual WAN ou utilizar o Parceiro Virtual WAN CPE (SD-WAN/VPN) para configurar a conectividade com o Azure. Os parceiros VIRTUAIS WAN fornecem automatização para conectividade, que é a capacidade de exportar a informação do dispositivo para o Azure, baixar a configuração Azure e estabelecer conectividade com o hub Azure Virtual WAN. Para a conectividade Point-to-site/User VPN, apoiamos [o cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN ou cliente IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Pode desativar os centros totalmente malhados num WAN virtual?

O WAN virtual vem em dois sabores: Básico e Standard. Em BASIC Virtual WAN, os centros não são malhados. Num WAN Virtual Standard, os hubs são misturados e automaticamente ligados quando o WAN virtual é configurado pela primeira vez. O utilizador não precisa de fazer nada específico. O utilizador também não tem de desativar ou permitir que a funcionalidade obtenha centros de malha. O WAN virtual fornece-lhe muitas opções de encaminhamento para orientar o tráfego entre qualquer porta-voz (VNet, VPN ou ExpressRoute). Proporciona a facilidade de centros totalmente malhados, bem como a flexibilidade do tráfego de encaminhamento de acordo com as suas necessidades. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Como são tratadas as Zonas de Disponibilidade e a resiliência em WAN Virtual?

Virtual WAN é uma coleção de hubs e serviços disponibilizados dentro do hub. O utilizador pode ter o maior NÚMERO Virtual WAN por sua necessidade. Num hub virtual WAN, existem vários serviços como VPN, ExpressRoute etc. Cada um destes serviços (exceto o Azure Firewall) é implantado numa região de Zonas de Disponibilidade, isto é, se a região apoiar Zonas de Disponibilidade. Se uma região se tornar uma Zona de Disponibilidade após a implantação inicial no hub, o utilizador pode recriar os gateways, o que irá desencadear uma implementação da Zona de Disponibilidade. Todos os gateways são a provisionados num centro como ativo, o que implica que há resiliência dentro de um centro. Os utilizadores podem ligar-se a vários centros se quiserem resiliência em todas as regiões. Embora o conceito de VIRTUAL WAN seja global, o recurso virtual WAN real é baseado em Gestor de Recursos e implantado regionalmente. Se a região wan virtual em si tiver um problema, todos os hubs nesse WAN virtual continuarão a funcionar como está, mas o utilizador não será capaz de criar novos hubs até que a região virtual WAN esteja disponível.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Que cliente suporta o suporte do utilizador Azure Virtual WAN VPN (Ponto a local)?

A WAN virtual suporta [o cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)o Cliente OpenVPN ou qualquer cliente IKEv2. A autenticação AZure AD é suportada com O Cliente Azure VPN.É necessário um mínimo da versão OS do cliente Windows 10 17763.0 ou superior.  Os clientes(s) OpenVPN podem suportar a autenticação baseada em certificados. Assim que o auth baseado em cert for selecionado no gateway, verá o ficheiro *.ovpn* para descarregar no seu dispositivo. O IKEv2 suporta a autenticação do certificado e do RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para a VPN do utilizador (ponto a local)- Porque é que a piscina de clientes P2S está dividida em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece para que cada instância de gateway possa alocar iPs de clientes independentemente para clientes conectados e o tráfego da rede virtual é encaminhado de volta para a instância de gateway correta para evitar o lúpulo de instância inter-gateway.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como adiciono servidores DNS para clientes P2S?

Existem duas opções para adicionar servidores DNS para os clientes P2S. O primeiro método é preferido, uma vez que adiciona os servidores DNS personalizados ao gateway em vez do cliente.

1. Utilize o seguinte script PowerShell para adicionar os servidores DNS personalizados. Substitua os valores para o seu ambiente.

   ```powershell
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
2. Ou, se estiver a utilizar o Cliente VPN Azure para o Windows 10, **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** pode modificar o perfil descarregado do ficheiro XML e adicionar as etiquetas antes de o importar.

   ```powershell
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

Cada gateway P2S do Utilizador tem duas instâncias e cada instância suporta certos utilizadores à medida que a unidade de escala muda. A unidade de escala 1-3 suporta 500 ligações, a unidade de escala 4-6 suporta 1000 ligações, a unidade de escala 7-12 suporta 5000 ligações e a unidade de escala 13-18 suporta até 10.000 ligações. 

Como exemplo, digamos que o utilizador escolhe uma unidade de escala de 1. Cada unidade de escala implicaria um gateway ativo implantado e cada uma das instâncias (neste caso 2) suportaria até 500 ligações. Uma vez que pode obter 500 ligações * 2 por gateway, não significa que planeie 1000 em vez dos 500 para esta unidade de escala. Podem ser necessários serviços durante os quais a conectividade para os 500 extras pode ser interrompida se ultrapassar a contagem de ligação recomendada. Além disso, certifique-se de planear o tempo de inatividade no caso de decidir escalar para cima ou para baixo na unidade de escala, ou alterar a configuração ponto-a-local no gateway VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual Azure (VPN Gateway) e um gateway Azure Virtual WAN VPN?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. Quando liga um site a um gateway Virtual WAN VPN, é diferente de um gateway de rede virtual regular que utiliza um tipo de gateway 'VPN'. Da mesma forma, quando liga um circuito ExpressRoute a um hub Virtual WAN, utiliza um recurso diferente para o gateway ExpressRoute do que o gateway de rede virtual regular que utiliza o tipo de gateway 'ExpressRoute'. 

O WAN virtual suporta até 20 Gbps de produção agregada tanto para VPN como ExpressRoute. A WAN virtual também tem automatização para conectividade com um ecossistema de parceiros de dispositivos de filial CPE. Os dispositivos de filial CPE têm automatização incorporada que autoprovisione e conecta-se ao Azure Virtual WAN. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [Lista de Parceiros Preferidos.](../articles/virtual-wan/virtual-wan-locations-partners.md)

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como é que o VIRTUAL WAN é diferente de um gateway de rede virtual Azure?

Uma VPN de gateway de rede virtual está limitada a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Pode ligar até 1.000 ligações de ramificação por região (hub virtual) com um agregado de 20 Gbps por hub. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Você pode ter um hub por região, o que significa que você pode conectar mais de 1.000 ramos através de centros.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma Unidade de Escala de Gateway VIRTUAL WAN

Uma unidade de escala é uma unidade definida para escolher uma produção agregada de um gateway no centro virtual. 1 unidade de escala de VPN = 500 Mbps. 1 unidade de escala de ExpressRoute = 2 Gbps. Exemplo: 10 unidades de VPN à escala implicariam 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais os fornecedores de dispositivos (parceiros VIRTUAIS WAN) são suportados?

Atualmente, muitos parceiros suportam a experiência de WAN Virtual totalmente automatizada. Para obter mais informações, veja [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são os passos de automatização dos parceiros da WAN Virtual?

Para obter os passos de automatização, veja [Automatização dos parceiros da WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1. A VIRTUAL WAN também tem soluções parceiras CPE que automatizam a conectividade com a Azure Virtual WAN, facilitando a configuração de ligações VPN IPsec em escala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como é que os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

As soluções de conectividade definida pelo software gerem, normalmente, os respetivos dispositivos de ramo com um controlador ou um centro de aprovisionamento de dispositivos. O controlador pode utilizar APIs do Azure para automatizar a conectividade à WAN Virtual do Azure. A automatização inclui o upload de informações do ramo, o descarregamento da configuração Azure, a criação de túneis IPSec em centros virtuais Azure e a configuração automática da conectividade do dispositivo de ramo para Azure Virtual WAN. Quando se tem centenas de agências, a ligação utilizando parceiros VIRTUAIS WAN CPE é fácil porque a experiência de embarque retira a necessidade de configurar, configurar e gerir a conectividade IPsec em larga escala. Para obter mais informações, consulte [a automatização de parceiros Virtual WAN.](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou a usar não estiver na lista de parceiros do Wan Virtual? Ainda posso usá-lo para ligar ao Azure Virtual WAN VPN?

Sim, desde que o dispositivo suporte iPsec IKEv1 ou IKEv2. Os parceiros WAN virtuais automatizam a conectividade do dispositivo para os pontos finais Azure VPN. Isto implica automatizar etapas como 'branch information upload', 'IPsec e configuração' e 'conectividade'. Como o seu dispositivo não é de um ecossistema parceiro Virtual WAN, terá de fazer o levantamento pesado de tomar manualmente a configuração Azure e atualizar o seu dispositivo para configurar a conectividade IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Todas as APIs wan virtuais estão abertas API. Pode ver a documentação [A automatização do parceiro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) para avaliar a viabilidade técnica. Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec. Uma vez concluída a empresa o trabalho de automatização do seu dispositivo CPE com base nas diretrizes de automatização acima fornecidas, pode contactar azurevirtualwan@microsoft.com para ser listado aqui Conectividade através de [parceiros.]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners) Se é um cliente que gostaria que uma determinada solução da empresa fosse listada como um parceiro Virtual WAN, por favor contacte a empresa contacte a WAN Virtual enviando um e-mail para azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como é que o WAN virtual suporta dispositivos SD-WAN?

Os parceiros virtuais wan automatizam a conectividade IPsec para os pontos finais do Azure VPN. Se o parceiro Virtual WAN for um fornecedor SD-WAN, então está implícito que o controlador SD-WAN gere a automação e a conectividade IPsec com os pontos finais do Azure VPN. Se o dispositivo SD-WAN necessitar do seu próprio ponto final em vez de Azure VPN para qualquer funcionalidade proprietária da SD-WAN, pode implantar o ponto final SD-WAN num VNet Azure e coexistir com a Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem ligar-se a um único hub?

Até 1.000 ligações são suportadas por hub virtual. Cada ligação é constituída por quatro ligações e cada ligação suporta dois túneis que se encontram numa configuração ativa. Os túneis terminam num gateway VPN do hub virtual de Azure. As ligações representam a ligação física ISP no dispositivo branch/VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma ligação de ramo ao Azure Virtual WAN?

Uma ligação de um ramo ou dispositivo VPN para Azure Virtual WAN não passa de uma ligação VPN que liga virtualmente o Site VPN e o Gateway VPN Azure num hub virtual.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Yes. O fluxo de tráfego, quando começa, é do dispositivo no local para a borda de rede mais próxima da Microsoft e, em seguida, para o centro virtual.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existem novos recursos do Resource Manager disponíveis para a WAN Virtual?
  
Sim, a VIRTUAL WAN tem novos recursos de Gestor de Recursos. Para obter mais informações, veja a [Descrição Geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar um Aparelho Virtual de Rede dentro do centro virtual?

Um aparelho virtual de rede (NVA) não pode ser implantado dentro de um centro virtual. No entanto, pode criá-lo num VNet falado que está ligado ao hub virtual e permitir o encaminhamento adequado para o tráfego direto de acordo com as suas necessidades.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Um VNet falado pode ter uma porta de entrada de rede virtual?

Não. O VNet falado não pode ter uma porta de entrada de rede virtual se estiver ligado ao centro virtual.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Existe apoio para o BGP na conectividade VPN?

Sim, o BGP é suportado. Quando criar um site VPN, pode fornecer os parâmetros BGP nele. Isto implicará que quaisquer ligações criadas em Azure para esse site serão ativadas para bGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?

Yes. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de um WAN Virtual com um hub e um vpnsite pode ser criada usando um [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN virtual é principalmente um serviço DE REST ou portal.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Os VNets falados ligados a um hub virtual podem comunicar entre si (V2V Transit)?

Yes. O STANDARD Virtual WAN suporta a conectividade transitiva VNet-to-VNet através do hub VIRTUAL WAN a que os VNets estão ligados. Na terminologia virtual WAN, referimo-nos a estes caminhos como "trânsito virtual local de WAN VNet" para VNets ligados a um Centro De Wan Virtual dentro de uma única região, e "global de trânsito virtual WAN VNet" para VNets conectados através de múltiplos Hubs VIRTUAIS WAN em duas ou mais regiões. Para alguns cenários, os VNets falados também podem ser diretamente espreitados uns com os outros usando [o Virtual Network Peering,](../articles/virtual-network/virtual-network-peering-overview.md) além do trânsito virtual WAN VNet local ou global. Neste caso, o VNet Peering tem precedência sobre a ligação transitiva através do hub VIRTUAL WAN.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual. O ramo é conceptualmente aplicável aos utilizadores VPN Site, ExpressRoute ou utilizadores point-to-site/user VPN. Permitir que o ramo seja ramificado é ativado por predefinição e pode ser localizado nas definições de configuração WAN. Isto permite que os balcões/utilizadores VPN se conectem a outros balcões VPN, bem como a conectividade de trânsito está ativada entre utilizadores VPN e ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de ramo-a-ramo atravessa o Azure Virtual WAN?

Yes.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>O WAN virtual requer o ExpressRoute de cada site?

Não. O WAN virtual não requer o ExpressRoute de cada site. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para sites que estão conectados usando ExpressRoute para um hub virtual, bem como IPsec VPN no mesmo hub, o hub virtual fornece conectividade de trânsito entre o utilizador VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Existe um limite de produção ou ligação de rede ao utilizar o Azure Virtual WAN?

A produção de rede é per serviço num hub WAN virtual. Enquanto você pode ter o número de WANs virtuais que você quiser, cada WAN Virtual permite 1 hub por região. Em cada hub, a produção agregada VPN é de até 20 Gbps, a produção agregada ExpressRoute é de até 20 Gbps e o rendimento agregado VPN/VPN do utilizador é de até 20 Gbps. O router em hub virtual suporta até 50 Gbps para fluxos de tráfego VNet-to-VNet e assume um total de 2000 VM de carga de trabalho em todos os VNets ligados a um único Hub virtual.

Quando os Sites VPN se ligam a um hub, fazem-no com ligações. O WAN virtual suporta até 1000 ligações ou túneis 2000 IPsec por centro virtual. Quando os utilizadores remotos se ligam ao hub virtual, conectam-se ao gateway P2S VPN, que suporta até 10.000 utilizadores dependendo da unidade de escala (largura de banda) escolhida para o gateway P2S VPN no centro virtual.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual é a produção total de VPN de um túnel VPN e uma ligação?

A produção total de VPN de um hub é de até 20 Gbps com base na unidade de escala escolhida do gateway VPN. A produção é partilhada por todas as ligações existentes. Cada túnel numa ligação pode suportar até 1 Gbps.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Posso usar o NAT-T nas minhas ligações VPN?
Sim, nat traversal (NAT-T) é suportado. Virtual WAN VPN Gateway NÃO executará qualquer funcionalidade semelhante ao NAT nos pacotes internos de/para os túneis IPsec. Nesta configuração, certifique-se de que o dispositivo no local inicia o túnel IPSec.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Não vejo a definição de 20 Gbps para o centro virtual no portal. Como posso configurar isto?

Navegue até ao gateway VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a definição apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>O VIRTUAL WAN permite que o dispositivo no local utilize vários ISPs em paralelo, ou é sempre um único túnel VPN?

As soluções de dispositivos no local podem aplicar políticas de tráfego para orientar o tráfego através de vários túneis para o hub Azure Virtual WAN (gateway VPN no centro virtual).

### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura global de trânsito, consulte [a arquitetura da rede de trânsito Global e a Virtual WAN.](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramo ->ISP->Microsoft rede edge->Microsoft edge->Microsoft DC (hub VNet)->dispositivo >de >de rede da Microsoft

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Yes. Uma ligação à Internet e dispositivo físico que suporta o IPsec, de preferência dos nossos [parceiros integrados virtual WAN.](../articles/virtual-wan/virtual-wan-locations-partners.md) Opcionalmente, pode gerir manualmente a configuração e conectividade com o Azure a partir do seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como posso permitir a rota predefinidora (0.0.0.0/0) numa ligação (VPN, ExpressRoute ou Rede Virtual)?

Um hub virtual pode propagar uma rota padrão aprendida para uma ligação VPN/ExpressRoute de rede virtual/local se a bandeira estiver 'Activada' na ligação. Esta bandeira é visível quando o utilizador edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. Por predefinição, esta bandeira é desativada quando um site ou um circuito ExpressRoute estão ligados a um hub. É ativado por padrão quando uma ligação de rede virtual é adicionada para ligar um VNet a um hub virtual. A rota predefinida não tem origem no hub Virtual WAN; a rota padrão é propagada se já for aprendida pelo hub Virtual WAN como resultado da implantação de uma firewall no centro, ou se outro site conectado tiver um túnel forçado habilitado. Uma rota predefinida não se propaga entre os hubs (inter-hub).

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Como é que o hub virtual num WAN virtual seleciona o melhor caminho para uma rota a partir de vários hubs

Se um Virtual Hub aprender a mesma rota a partir de vários hubs remotos, a ordem pela qual decide é a seguinte:

1. O prefixo mais longo.
2. Rotas locais sobre o interhub (Centro Virtual atribui 65520-65520 para o interhub AS)
3. Rotas estáticas sobre o BGP: Isto está em contexto para a decisão que está a ser tomada pelo router Virtual Hub. No entanto, se o decisor for o gateway VPN onde um site anuncia rotas via BGP ou fornece prefixos de endereço estático, as rotas estáticas podem ser preferíveis nas rotas BGP.
4. ExpressRoute (ER) sobre VPN: ER é preferido sobre VPN quando o contexto é um hub local. A conectividade de trânsito entre os circuitos ExpressRoute só está disponível através do Global Reach. Portanto, em cenários em que o circuito ExpressRoute está ligado a um hub e há outro circuito ExpressRoute ligado a um centro diferente com ligação VPN, a VPN pode ser preferível para cenários inter-hub.
5. Comprimento do caminho as.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>O hub VIRTUAL WAN permite a conectividade entre os circuitos ExpressRoute.

O trânsito entre o ER-ER é sempre via Alcance Global. Gateways de hub virtuais são implantados nas regiões de DC ou Azure. Quando dois circuitos ExpressRoute se ligam via Alcance Global, não há necessidade de o tráfego vir dos routers de borda para o centro virtual DC.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Existe um conceito de peso nos circuitos Azure Virtual WAN ExpressRoute ou ligações VPN

Quando vários circuitos ExpressRoute estão ligados a um hub virtual, o peso do encaminhamento na ligação fornece um mecanismo para que o ExpressRoute no centro virtual prefira um circuito em vez do outro. Não há mecanismo para definir um peso numa ligação VPN. O Azure prefere sempre uma ligação ExpressRoute em vez de uma ligação VPN dentro de um único hub.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Será que a Virtual WAN prefere o ExpressRoute em vez da VPN para a saída de tráfego do Azure

Yes.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Quando um hub Virtual WAN tem um circuito ExpressRoute e um Site VPN ligado a ele, o que faria com que uma rota de ligação VPN fosse preferida em vez do ExpressRoute?

Quando um circuito ExpressRoute está ligado ao hub virtual, os routers de borda da Microsoft são o primeiro nó para a comunicação entre as instalações e o Azure. Estes routers de borda comunicam com os gateways Virtual WAN ExpressRoute que, por sua vez, aprendem rotas a partir do router de hub virtual que controla todas as rotas entre quaisquer gateways em VIRTUAL WAN. Os routers de borda da Microsoft processam rotas de hub virtual ExpressRoute com maior preferência sobre as rotas aprendidas a partir das instalações. Devido a qualquer razão, se a ligação VPN se tornar o meio principal para o hub virtual aprender rotas (por exemplo, cenários de failover entre ExpressRoute e VPN), a menos que o VPN Site tenha um comprimento DE CAMINHO MAIS longo, o hub virtual continuará a partilhar rotas aprendidas VPN com o gateway ExpressRoute, fazendo com que os routers microsoft Edge prefiram rotas VPN sobre as rotas nas instalações.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Quando dois hubs (hub 1 e 2) estão ligados e há um circuito ExpressRoute ligado como um laço a ambos os centros, qual é o caminho para um VNet ligado ao hub 1 alcançar um VNet ligado no centro 2?

O comportamento atual é preferir o caminho do circuito ExpressRoute em vez do hub-para-hub para a conectividade VNet-to-VNet. No entanto, isto não é encorajado numa configuração wan virtual. A equipa virtual WAN está a trabalhar numa correção para permitir a preferência pelo hub-to-hub sobre o caminho ExpressRoute. A recomendação é que vários circuitos ExpressRoute (diferentes fornecedores) se conectem a um hub e utilizem a conectividade hub-to-hub fornecida pela Virtual WAN para fluxos de tráfego inter-região.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Os hubs podem ser criados em diferentes grupos de recursos em VIRTUAL WAN?
Yes. Esta opção está atualmente disponível apenas através do PowerShell. O portal VIRTUAL WAN determina os hubs no mesmo grupo de recursos que o próprio recurso Virtual WAN.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Existe suporte para o IPv6 em VIRTUAL WAN?

O IPv6 não é suportado no centro VIRTUAL WAN e nas suas portas de entrada. Se tem um VNet que tem suporte IPv4 e IPv6 e gostaria de ligar o VNet ao WAN Virtual, este cenário não está atualmente suportado. 

Para o cenário VPN do site (utilizador) com a fuga de internet através do Azure Firewall, é provável que tenha de desligar a conectividade IPv6 no dispositivo do seu cliente para forçar o tráfego ao centro de WAN virtual. Isto porque os dispositivos modernos por padrão usam endereços IPv6 por padrão.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Qual é a versão API recomendada para ser usada por scripts automatizando várias funcionalidades de WAN Virtual?

É necessária uma versão mínima de 05-01-2020 (1 de maio de 2020). 

### <a name="are-there-any-virtual-wan-limits"></a>Existem limites de WAN virtuais?

Consulte a secção [de limites DE WAN Virtual](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) na página de limites de subscrição e serviço.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN Virtual (Básico e Standard)?

Ver [WANs Virtuais Básicos e Padrão.](../articles/virtual-wan/virtual-wan-about.md#basicstandard) Para obter preços, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/virtual-wan/)

### <a name="does-virtual-wan-store-customer-data"></a>A VIRTUAL WAN armazena os dados do cliente? 

Não. A WAN virtual não armazena quaisquer dados do cliente.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Existem fornecedores de serviços geridos que possam gerir o WAN virtual para os utilizadores como um serviço? 

Yes. Para obter uma lista de soluções do Managed Service Provider (MSP) habilitados através do Azure Marketplace, consulte as ofertas do [Azure Marketplace pelos parceiros MSP da Azure Networking Networking](../articles/networking/networking-partners-msp.md#msp).
