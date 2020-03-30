---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ad821036047dcf46821b2b2722e3dd17f8e318c2
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386127"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O utilizador precisa de ter hub e falou com dispositivos SD-WAN/VPN para utilizar o Azure Virtual WAN?

O VIRTUAL WAN fornece muitas funcionalidades incorporadas numa única vidraça de vidro, como conectividade VPN site/site-para-site, conectividade User/P2S, conectividade ExpressRoute, conectividade Rede Virtual, Interconectividade VPN ExpressRoute, VNET para VNET transitivo conectividade, Routing Centralizado, firewall Azure e segurança do gestor de firewall, Monitorização, Encriptação ExpressRoute e muitas outras capacidades. Não é preciso ter todos estes casos de utilização para começar a usar O WAN Virtual. Pode simplesmente começar com apenas um caso de uso. A arquitetura Virtual WAN é um hub e uma arquitetura falada com escala e desempenho integrado onde sucursais (dispositivos VPN/SD-WAN), utilizadores (Clientes Azure VPN, clientes openVPN ou IKEv2), circuitos ExpressRoute, Redes Virtuais servem de porta-voz esportivas ao Virtual Hub(s). Todos os hubs estão ligados em malha completa num Wan Virtual Standard, facilitando ao utilizador a utilização da espinha dorsal da Microsoft para qualquer (qualquer)conectividade (qualquer falado). Para o hub e falou com dispositivos SD-WAN/VPN, os utilizadores podem instalá-lo manualmente no portal Bluee Virtual WAN ou usar o CPE virtual WAN Partner (SD-WAN/VPN) para estabelecer conectividade com o Azure. Os parceiros virtuais wan fornecem automação para conectividade que é a capacidade de exportar a informação do dispositivo para o Azure, baixar a configuração Do Azure e estabelecer conectividade com o hub Bluee Virtual WAN. Para a conectividade Point-to-site/User VPN, apoiamos o [cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)o cliente OpenVPN ou IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Qual é o suporte do utilizador Virtual WAN VPN (Ponto-a-local) do Azure Virtual WAN?

O Virtual WAN suporta [o cliente Azure VPN,](https://go.microsoft.com/fwlink/?linkid=2117554)o Cliente OpenVPN ou qualquer cliente IKEv2. A autenticação Azure AD é suportada com o Cliente Azure VPN.É necessário um mínimo de cliente do Windows 10 versão OS 17763.0 ou superior.  O(s) cliente(s) OpenVPN pode apoiar a autenticação baseada em certificados. Uma vez selecionado o auth baseado em cert no portal, você verá o ficheiro .ovpn para descarregar para o seu dispositivo. Tanto o certificado como o radius auth são suportados com IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para o User VPN (Ponto-a-local)- Porque é que a piscina de clientes P2S está dividida em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece de modo que cada instância de gateway pode atribuir iPs de cliente de forma independente para clientes conectados e o tráfego da rede virtual é encaminhado de volta para a instância correta de gateway para evitar o salto de instância intergateway.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como adiciono servidores DNS para clientes P2S?

Existem duas opções para adicionar servidores DNS para os clientes P2S.

1. Abra um bilhete de suporte com a Microsoft e mande-os adicionar os seus servidores DNS ao hub
2. Ou, se estiver a utilizar o Cliente VPN Azure para o Windows 10, pode modificar o ficheiro XML de perfil descarregado e adicionar os ** \<dnsservers \<>dnsserver>/dnsserver \<>\</dnsserver s>** tags antes de o importar.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Para o User VPN (Ponto-a-site)- quantos clientes são suportados?

Cada gateway VPN P2S do utilizador tem duas instâncias e cada instância suporta até certos utilizadores à medida que a unidade de escala muda. A unidade de escala 1-3 suporta 500 ligações, a unidade de escala 4-6 suporta 1000 ligações, a unidade de escala 7-10 suporta 5000 ligações e a unidade de escala 11+ suporta até 10.000 ligações. Como exemplo, digamos que o utilizador escolhe uma unidade de escala. Cada unidade de escala implicaria uma porta ativa implantada e cada uma das instâncias (neste caso 2) suportaria até 500 ligações. Uma vez que pode obter 500 ligações * 2 por gateway, não significa que planeie 1000 em vez dos 500 para esta unidade de escala, pois os casos podem precisar de ser reparados durante os quais a conectividade para o extra 500 pode ser interrompida se ultrapassar a contagem de ligação recomendada.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual Azure (VPN Gateway) e um gateway Azure Virtual WAN VPN?

A WAN Virtual oferece conectividade site a site em grande escala e foi criada para débito, escalabilidade e facilidade de utilização. Quando liga um site a um gateway Virtual WAN VPN, é diferente de um gateway de rede virtual regular que utiliza um tipo de gateway 'VPN'. Da mesma forma, quando liga um circuito ExpressRoute a um hub Virtual WAN, utiliza um recurso diferente para o gateway ExpressRoute do que o gateway de rede virtual regular que utiliza o tipo de gateway 'ExpressRoute'. O WAN virtual suporta até 20 Gbps de produção agregada tanto para VPN como ExpressRoute. O WAN virtual também tem automação para a conectividade com um ecossistema de parceiros de dispositivos de ramificação CPE. Os dispositivos de ramificação CPE têm automatização incorporada que aprovisiona automaticamente e liga-se ao Azure Virtual WAN. Estes dispositivos estão disponíveis num ecossistema crescente de parceiros de SD-WAN e de VPN. Consulte a [Lista de Parceiros Preferenciais](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como é que o Wan Virtual é diferente de um portal de rede virtual Azure?

Um gateway de rede virtual VPN está limitado a 30 túneis. Para as conexões, deve utilizar a WAN Virtual para uma VPN de grande escala. Pode ligar até 1.000 ligações de ramificação por região (centro virtual) com um agregado de 20 Gbps por hub. Uma ligação é um túnel ativo-ativo do dispositivo VPN no local para o hub virtual. Você pode ter um centro por região, o que significa que você pode conectar mais de 1.000 ramos através de centros.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma Unidade virtual de escala wan gateway
Uma unidade de escala é uma unidade definida para escolher uma entrada agregada de um gateway no centro virtual. 1 unidade de escala de VPN = 500 Mbps . 1 unidade de escala da ExpressRoute = 2 Gbps. Exemplo: 10 unidade de escala de VPN implicaria 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Que fornecedores de dispositivos (parceiros Virtual WAN) são suportados?

Atualmente, muitos parceiros suportam a experiência de WAN Virtual totalmente automatizada. Para obter mais informações, veja [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são os passos de automatização dos parceiros da WAN Virtual?

Para obter os passos de automatização, veja [Automatização dos parceiros da WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Sou obrigado a utilizar um dispositivo de parceiro preferencial?

Não. Pode utilizar qualquer dispositivo compatível com VPN que cumpra os requisitos para suporte de IPsec de IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como é que os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

As soluções de conectividade definida pelo software gerem, normalmente, os respetivos dispositivos de ramo com um controlador ou um centro de aprovisionamento de dispositivos. O controlador pode utilizar APIs do Azure para automatizar a conectividade à WAN Virtual do Azure. A automatização inclui o upload de informações do ramo, o download da configuração Azure, a instalação de túneis IPSec em hubs Azure Virtual e a configuração automática da conectividade do dispositivo de ramificação para o Azure Virtual WAN. Quando se tem centenas de balcões, ligar-se utilizando parceiros virtuais DE WAN CPE é fácil porque a experiência de embarque tira a necessidade de configurar, configurar e gerir a conectividade IPsec em larga escala. Para mais informações, consulte a automatização de [parceiros Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como é que o Virtual WAN suporta dispositivos SD-WAN?

Os parceiros virtuais WAN automatizam a conectividade IPsec com os pontos finais da VPN Azure. Se o parceiro Virtual WAN é um fornecedor SD-WAN, então está implícito que o controlador SD-WAN gere a automação e a conectividade IPsec com os pontos finais da VPN Azure. Se o dispositivo SD-WAN necessitar do seu próprio ponto final em vez do Azure VPN para qualquer funcionalidade sd-WAN proprietária, pode implantar o ponto final SD-WAN num Azure VNet e coexistir com o Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera funcionalidades de conectividade existentes?

As funcionalidades de conectividade do Azure existentes não sofrem quaisquer alterações.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existem novos recursos do Resource Manager disponíveis para a WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Resource Manager. Para obter mais informações, veja a [Descrição Geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem ligar a um único hub?

Até 1.000 ligações são suportadas por centro virtual. Cada ligação consiste em quatro ligações e cada ligação de ligação suporta dois túneis que se encontram numa configuração ativa. Os túneis terminam num hub virtual azure vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN no local pode ser ligado a vários Hubs?

Sim. O fluxo de tráfego, quando começa, é do dispositivo no local até à borda mais próxima da rede da Microsoft e, em seguida, ao centro virtual.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implementar e utilizar a minha aplicação virtual de rede favorita (numa VNet de NVA) com a WAN Virtual do Azure?

Sim, pode ligar a VNet da aplicação virtual de rede (NVA) favorita à WAN Virtual do Azure. Em primeiro lugar, ligue a VNet da aplicação de rede virtual ao hub com uma ligação de Rede Virtual do Hub. Em seguida, crie uma rota de hub virtual com um próximo salto apontando para o Aparelho Virtual. Pode aplicar várias rotas para a Tabela de Rota do Hub virtual. Quaisquer spokes ligados à VNet NVA têm de estar ligados adicionalmente ao hub virtual para garantir que as rotas de VNet do spoke são propagadas para sistemas no local.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar um Aparelho Virtual rede dentro do centro virtual?

Um aparelho virtual de rede (NVA) não pode ser implantado dentro de um centro virtual. No entanto, pode criá-lo num VNet falado que está ligado ao hub virtual e permitir uma rota no centro para direcionar o tráfego para o destino VNet através do endereço IP NVA (do NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Um VNet falado pode ter um portal de rede virtual?

Não. O VNet falado não pode ter um portal de rede virtual se estiver ligado ao centro virtual.

### <a name="is-there-support-for-bgp"></a>Existe suporte para BGP?

Sim, o BGP é suportado. Ao criar um site VPN, pode fornecer os parâmetros BGP nele. Isto implicará que quaisquer ligações criadas em Azure para esse site serão ativadas para o BGP. Além disso, se você tinha um VNet com um NVA, e se este NVA VNet foi ligado a um hub Virtual WAN, de forma a garantir que as rotas de um VNet NVA são publicitadas adequadamente, os porta-vozes ligados à NVA VNet devem desativar o BGP. Além disso, ligue estes VNets falados ao centro virtual VNet para garantir que as rotas VNet faladas são propagadas para sistemas no local.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego com um UDR no hub virtual?

Sim, pode direcionar o tráfego para um VNet usando uma mesa de rota de hub virtual. Isto permite-lhe definir rotas para vNets de destino em Azure através de um endereço IP específico (tipicamente do NVA NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Existem informações de licenciamento ou de preços da WAN Virtual?

Sim. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como calculo o preço de um centro?

* Pagaria pelos serviços no centro. Por exemplo, digamos que tem 10 balcões ou dispositivos no local que exigem a ligação ao Azure Virtual WAN implicaria a ligação aos pontos finais VPN no centro. Digamos que esta é VPN de 1 unidade de escala = 500 Mbps, isto é cobrado a $0.361/h. Cada ligação é cobrada a $0,05/h. Para 10 ligações, o custo total do serviço/hr seria $0.361 + $.5/hr. Aplicam-se os encargos com os dados relativos ao tráfego que sai do Azure.

* Há uma carga adicional no centro. Veja a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Se tivesse o gateway ExpressRoute devido aos circuitos ExpressRoute que ligavam a um hub virtual, então pagaria pelo preço unitário de escala. Cada unidade de escala em ER é de 2 Gbps e cada unidade de ligação é carregada ao mesmo ritmo que a unidade vpn connection.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como é que os novos parceiros que não estão listados na sua lista de parceiros de lançamento são incluídos?

Todas as APIs wan virtuais estão abertas a API. Pode ver a documentação para avaliar a viabilidade técnica. Se tiver alguma dúvida, envie azurevirtualwan@microsoft.comum e-mail para . Um parceiro ideal é o que tem um dispositivo que pode ser aprovisionado para a conectividade IKEv1 ou IKEv2 IPsec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou a usar não estiver na lista de parceiros virtual wan? Ainda posso usá-lo para ligar à VPN Wan Virtual Do Azure?

Sim, desde que o dispositivo suporte iPsec IKEv1 ou IKEv2. Os parceiros virtuais WAN automatizam a conectividade do dispositivo para os pontos finais da VPN Azure. Isto implica automatizar etapas como o "upload de informação de ramificação", "IPsec e configuração" e "conectividade". Uma vez que o seu dispositivo não é de um ecossistema parceiro Virtual WAN, terá de fazer o levantamento pesado da tomada manual da configuração Azure e atualizar o seu dispositivo para configurar a conectividade IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir uma WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de um WAN virtual com um hub e um vpnsite pode ser criado usando um [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). O WAN virtual é principalmente um serviço de REPOUSO ou portal.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O peering de VNet Global é suportado com a WAN Virtual do Azure? 

Pode ligar um VNet numa região diferente do seu WAN virtual.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Os VNets falados podem comunicar entre si um centro virtual (V2V Transit)?

Sim. O Standard Virtual WAN suporta a conectividade transitiva Vnet para Vnet através do centro virtual WAN a que os Vnets estão ligados. Na terminologia Virtual WAN, referimo-nos a estes caminhos como "trânsito virtual WAN VNet" para VNets ligados a um Virtual Wan Hub dentro de uma única região, e "trânsito global de WAN VNet" para VNets conectados através de múltiplos Centros WAN Virtuais em dois ou mais regiões. O trânsito vNet suporta até 3 Gbps de entrada durante a pré-visualização pública. A produção expandir-se-á quando o trânsito global for a GA.

NOTA: Atualmente, a pré-visualização do trânsito V2V requer que um GW VPN seja implantado num Hub Virtual para desencadear o lançamento dos elementos de encaminhamento. Este VPN GW não é utilizado para a via de trânsito V2V. Esta é uma limitação conhecida e será removida no momento do V2V GA. Pode eliminar o Gateway VPN no centro(s) depois de totalmente lançado, uma vez que não é necessário para a funcionalidade de trânsito V2V. 

Para alguns cenários, os Vnets falados também podem ser diretamente espreitados uns com os outros usando o [Virtual Network Peering,](../articles/virtual-network/virtual-network-peering-overview.md) além do trânsito virtual wan VNet local ou global. Neste caso, a Vnet Peering tem precedência sobre a ligação transitiva através do centro virtual WAN. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma ligação de ramo ao Azure Virtual WAN?

Uma ligação de um dispositivo de ramificação para o Azure Virtual WAN suporta até quatro links. Uma ligação é a ligação de conectividade física na localização do ramo (por exemplo: ATT, Verizon, etc.). Cada ligação de ligação é composta por dois túneis IPsec ativos/ativos.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade entre ramificações é permitida na WAN Virtual?

Sim, a conectividade entre ramificações está disponível na WAN Virtual para VPN e VPN para o ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de ramificação a sucursal atravessa o Wan Virtual Azure?

Sim.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>O Virtual WAN requer a ExpressRoute de cada site?

Não, a WAN Virtual não precisa do ExpressRoute de cada site. Utiliza conectividade padrão iPsec site-to-site através de links de internet do dispositivo para um hub Bluee Virtual WAN. Os sites podem estar ligados a uma rede de fornecedor através de um circuito do ExpressRoute. Para sites que estão ligados usando expressRoute em um hub virtual, os sites podem ter ramificação para ramificar fluxo de tráfego entre VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há algum limite de débito de rede ao utilizar a WAN Virtual do Azure?

O número de balcões é limitado a 1000 ligações por hub/região e um total de 20 Gbps no centro. Pode ter 1 centro por região.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Quantas ligações VPN suporta um centro virtual WAN?

Um hub Azure Virtual WAN pode suportar até 1.000 ligações S2S, 10.000 ligações P2S e 4 ligações ExpressRoute simultaneamente.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual é a produção total de VPN de um túnel VPN e uma ligação?

A produção total de VPN de um hub é de até 20 Gbps com base na unidade de escala escolhida. A entrada é partilhada por todas as ligações existentes. Cada túnel numa ligação pode suportar até 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Não vejo a definição de 20 Gbps para o centro virtual no portal. Como posso configurar isto?

Navegue para o gateway VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a configuração apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>O Virtual WAN permite que o dispositivo no local utilize vários ISPs em paralelo, ou é sempre um único túnel VPN?

Uma ligação que entra num WAN VPN virtual é sempre um túnel ativo (para resiliência dentro do mesmo hub/região) usando um link disponível no ramo. Esta ligação pode ser uma ligação ISP na sucursal no local. Virtual WAN 'VPNSite' fornece a capacidade de adicionar informações de link ao site. Se tiver vários ISPs na sucursal e cada um dos ISPs fornecer um link, essa informação pode ser configurada na informação do site VPN em Azure. No entanto, gerir a falha através dos ISPs na sucursal é completamente uma operação de encaminhamento centrada em ramificações.

### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura global de trânsito?

Para obter informações sobre a arquitetura global de trânsito, consulte a arquitetura global da rede de trânsito e o [Wan Virtual.](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como é que o tráfego é encaminhado no backbone do Azure?

O tráfego segue o padrão: dispositivo de ramificação ->dispositivo de >rede ISP->Microsoft >Microsoft DC (hub VNet)->dispositivo de ramificação de >ISP >

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Neste modelo, do que precisa em cada site? Apenas uma ligação à internet?

Sim. Uma ligação à Internet e dispositivo físico que suporta o IPsec, de preferência dos [nossos parceiros integrados](../articles/virtual-wan/virtual-wan-locations-partners.md)de WAN Virtual. Opcionalmente, pode gerir manualmente a configuração e conectividade com o Azure a partir do seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como posso ativar a rota padrão (0.0.0.0/0) numa ligação (VPN, ExpressRoute ou Rede Virtual):

Um hub virtual pode propagar uma rota padrão aprendida para uma ligação VPN/ExpressRoute de rede virtual/local-para-site se a bandeira estiver 'Activada' na ligação. Esta bandeira é visível quando o utilizador edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. Por predefinição, esta bandeira é desativada quando um local ou um circuito ExpressRoute está ligado a um hub. É ativado por padrão quando uma ligação de rede virtual é adicionada para ligar um VNet a um hub virtual. A rota padrão não tem origem no centro virtual WAN; a rota padrão é propagada se já for aprendida pelo centro virtual WAN como resultado da implantação de uma firewall no centro, ou se outro local conectado tiver um túnel forçado ativado.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN virtual (Básico e Standard)?

O tipo WAN 'Basic' permite criar um hub básico (SKU = Básico). Um tipo 'Standard' WAN permite criar um hub padrão (SKU = Standard). Os centros básicos limitam-se à funcionalidade VPN site-to-site. Os hubs standard permitem-lhe ter ExpressRoute, User VPN (P2S), centro de malha completa e trânsito VNet-to-VNet através dos hubs. Você paga uma taxa base de $0,25/h para centros padrão e uma taxa de processamento de dados para transitar através dos hubs durante a conectividade VNet-to-VNet, bem como processamento de dados para o hub para hub para tráfego hub. Para mais informações, consulte [As NWaNs virtuais Básicas e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Para preços, consulte a página [de Preços.](https://azure.microsoft.com/pricing/details/virtual-wan/)
