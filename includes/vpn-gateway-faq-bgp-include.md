---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6054fe5f71f54794d4974a71cdfd61a7959534ff
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92082221"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP é suportado em todos os SKUs do VPN Gateway do Azure?
O BGP é suportado em todos os SKUs Azure VPN Gateway, exceto o Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Posso utilizar o BGP com gateways de VPN do Azure baseados em políticas?
Não, o BGP é apenas suportado em gateways de VPN baseados na rota.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>O que é que a ASN (Números do Sistema Autónomo) posso utilizar?
Você pode usar as suas próprias ASNs públicas ou ASNs privadas tanto para as suas redes no local como para redes virtuais Azure, **exceto** as gamas reservadas pela Azure ou pela IANA:

> [!IMPORTANT]
>
> As seguintes ASNs são reservadas pela Azure ou pela IANA:
> * ASNs reservadas por Azure:
>    * Public ASNs: 8074, 8075, 12076
>    * ASNs privados: 65515 65517, 65518, 65519, 65520
> * ASNs [reservadas pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496-64511, 65535-65551 e 429496729
>
> Não é possível especificar estas ASNs para os seus dispositivos VPN no local quando se ligar aos gateways Azure VPN.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Posso utilizar ASNs (4 bytes) de 32 bits (4 bytes)?
Sim, a Azure VPN Gateways agora suporta ASNs de 32 bits (4 bytes). Utilize powerShell/CLI/SDK para configurar utilizando a ASN em formato decimal.

### <a name="what-private-asns-can-i-use"></a>Que ASNs privada posso usar?
A gama utilizável de ASNs privadas que podem ser utilizadas são:

* 64512-65514, 65521-65534

Estas ASNs não são reservadas pela IANA ou pela Azure para utilização e, portanto, podem ser utilizadas para atribuir ao seu Gateway Azure VPN.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Que endereço utiliza o gateway de VPN do Azure para o IP do Elemento de Rede BGP?

* Por predefinição, o gateway Azure VPN irá atribuir um único endereço IP da gama GatewaySubnet para gateways VPN de standby ativo, ou dois endereços IP para gateways VPN ativos. Estes endereços são atribuídos automaticamente quando cria o gateway VPN. Pode obter o endereço IP real (es) atribuído utilizando o PowerShell (Get-AzVirtualNetworkGateway, procure a propriedade "bgpPeeringAddress"), ou no portal Azure (sob a propriedade "Configuração BGP ASN" na página de Configuração gateway).

* Se os seus routers VPN no local utilizarem endereços **IP APIPA** (169.254.x.x) como endereços IP BGP, deve especificar um **endereço IP Azure APIPA BGP** adicional no seu gateway Azure VPN. O gateway Azure VPN irá selecionar o endereço APIPA para utilizar com o par APIPA BGP no local especificado no gateway de rede local, ou endereço IP privado para não-APIPA no local BGP peer. Para mais informações, consulte [o Configure BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos dos endereços IP do Elemento de Rede BGP no meu dispositivo VPN?
O seu endereço de pares BGP no local **NÃO deve** ser o mesmo que o endereço IP público do seu dispositivo VPN ou do espaço de endereço VNet do Gateway VPN. Utilize um endereço IP diferente no dispositivo VPN do IP do Elemento de Rede BGP. Pode ser um endereço atribuído à interface loopback do dispositivo, seja um endereço IP regular ou um endereço APIPA. Se o seu dispositivo utilizar o endereço APIPA para BGP, tem de especificar um endereço IP APIPA BGP no seu gateway Azure VPN, conforme descrito no [Configure BGP](../articles/vpn-gateway/bgp-howto.md). Especifique este endereço no Gateway de Rede Local correspondente que representa a localização.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como prefixos de endereços para o Gateway de Rede Local ao utilizar o BGP?

> [!IMPORTANT]
>
>Trata-se de uma alteração da exigência previamente documentada. Se utilizar o BGP para uma ligação, deve deixar o campo **de espaço Address** ***vazio*** para o recurso de gateway de rede local correspondente. O gateway Azure VPN adicionará internamente uma rota de anfitrião para o IP de pares BGP no local sobre o túnel IPsec. **NÃO** adicione a rota /32 no campo espacial Address. É redundante e se utilizar um endereço APIPA como o dispositivo VPN no local BGP IP, não pode ser adicionado a este campo. Se adicionar quaisquer outros prefixos no campo espacial Address, serão adicionados como **rotas estáticas** no gateway Azure VPN, além das rotas aprendidas via BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Posso utilizar o mesmo ASN para as redes VPN no local e as VNets do Azure?
Não, tem de atribuir ASNs diferentes entre as suas redes no local e as VNets do Azure se estiver a ligá-las entre si com o BGP. Os Gateways de VPN do Azure têm uma predefinição ASN de 65515 atribuída,quer o BGP esteja ou não ativado para a conectividade em vários locais. Pode substituir esta predefinição atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois do gateway ter sido criado. Terá de atribuir os seus ASNs no local aos Gateways de Rede Local do Azure correspondentes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Que prefixos de endereços irão os gateways de VPN do Azure anunciar-me?
O gateway de VPN do Azure irá anunciar as seguintes rotas para os seus dispositivos BGP no local:

* Os seus prefixos de endereços VNet
* Prefixos de endereços para cada Gateway de Rede Local ligado ao gateway de VPN do Azure
* Rotas aprendidas de outras sessões de peering de BGP ligadas ao gateway de VPN do Azure, **exceto a rota ou as rotas predefinidas que se sobreponham a um prefixo VNet**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quantos prefixos posso anunciar ao gateway Azure VPN?
Apoiamos até 4000 prefixos. A sessão de BGP é ignorada se o número de prefixos exceder o limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota predefinida (0.0.0.0/0) para gateways de VPN do Azure?
Sim.

Note que isto forçará todo o tráfego de saídas VNet para o seu site no local, e impedirá que os VMs VNet aceitem a comunicação pública diretamente da Internet, tais como RDP ou SSH da Internet para os VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Posso anunciar os prefixos exatos como prefixos da minha Rede Virtual?

Não, o ato de anunciar os mesmos prefixos como prefixos de endereços da sua Rede Virtual será bloqueado ou filtrado pela plataforma do Azure. No entanto, pode anunciar um prefixo que é um conjunto mais amplo do que tem no interior da Rede Virtual. 

Por exemplo, se a sua rede virtual utilizou o espaço de endereços 10.0.0.0/16, pode anunciar 10.0.0.0/8. Contudo, não pode anunciar 10.0.0.0/16 ou 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Posso utilizar o BGP com as minhas ligações VNet a VNet?
Sim, pode utilizar o BGP para ligações em vários locais e ligações VNet a VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar ligações BGP com ligações não BGP para os meus gateways de VPN do Azure?
Sim, pode misturar ligações BGP e não BGP para o mesmo gateway de VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>O gateway de VPN do Azure suporta encaminhamento de trânsito BGP?
Sim, o encaminhamento de trânsito BGP é suportado. No entanto, os gateways de VPN do Azure **NÃO** anunciarão rotas predefinidas a outros elementos de rede BGP. Para ativar o encaminhamento de trânsito através de vários gateways de VPN do Azure, tem de ativar o BGP em todas as ligações VNet para VNet intermédias. Para mais informações, consulte [Sobre o BGP.](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais do que um túnel entre o gateway de VPN do Azure e a minha rede no local?
Sim, pode estabelecer mais do que um túnel VPN S2S entre um gateway de VPN do Azure e a sua rede no local. Note que todos estes túneis serão contados com o número total de túneis para os seus gateways Azure VPN e deve ativar bGP em ambos os túneis.

Por exemplo, se tiver dois túneis redundantes entre o seu gateway Azure VPN e uma das suas redes no local, eles consumirão 2 túneis fora da quota total para o seu gateway Azure VPN.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Posso ter vários túneis entre duas VNets do Azure com BGP?
Sim, mas pelo menos um dos gateways de rede virtual tem de estar na configuração de ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Posso utilizar o BGP para VPN S2S numa configuração de coexistência ExpressRoute/S2S VPN?
Sim. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar ao meu dispositivo VPN no local para a sessão de peering de BGP?
Deve adicionar uma rota de anfitrião do endereço IP do Elemento de Rede BGP do Azure no dispositivo VPN para apontar para o túnel VPN S2S IPsec. Por exemplo, se o IP do Elemento VPN do Azure for “10.12.255.30”, deve adicionar no dispositivo VPN uma rota de anfitrião para “10.12.255.30” com uma interface nexthop da interface de túnel IPsec correspondente.

### <a name="does-the-virtual-network-gateway-support-bidirectional-forwarding-detection-bfd-for-site-to-site-connections-with-bgp"></a>O Gateway de Rede Virtual suporta a Deteção bidirecional de encaminhamento (BFD) para ligações site-to-site com BGP?
Não. O Bidirectional Forwarding Detection (BFD) é um protocolo que pode ser usado ao longo do BGP para detetar o tempo de inatividade do vizinho mais rápido do que usar os keepalives BGP padrão. O BFD utiliza temporizadores de subsegundos concebidos para trabalhar em ambientes LAN, mas não em toda a internet pública ou ligações de rede de ampla área. Para ligações através da Internet pública, ter certos pacotes atrasados ou mesmo largados não é incomum, pelo que a introdução destes temporizadores agressivos acrescentaria instabilidade que poderia acabar por causar potencialmente o amortecimento das rotas pela BGP. Como alternativa, pode configurar o seu dispositivo no local com temporizadores inferiores ao intervalo de 60 segundos padrão e o temporizador de 180 segundos para um tempo de convergência mais rápido.
