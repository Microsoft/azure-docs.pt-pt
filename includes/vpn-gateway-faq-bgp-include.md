---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e3f996ab2a42057198368759c75f10e911d5f54
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68936875"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP é suportado em todos os SKUs do VPN Gateway do Azure?
Não, o BGP é suportado no Azure **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** e nos gateways de VPN **HighPerformance**. O SKU **Básico** NÃO é suportado.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Posso utilizar o BGP com gateways de VPN do Azure baseados em políticas?
Não, o BGP é apenas suportado em gateways de VPN baseados na rota.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Posso utilizar ASNs (Números de Sistema Autónomo) privados?
Sim, pode utilizar os seus próprios ASNs públicos ou privados para as suas redes no local e para redes virtuais do Azure.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>Posso utilizar ASNs (Números de Sistema Autónomo) de 32 bits?
Não, os Gateways de VPN do Azure suportam ASNs de 16 bits, hoje.

### <a name="are-there-asns-reserved-by-azure"></a>Existem ASNs reservados pelo Azure?
Sim, os seguintes ASNs são reservados pelo Azure para peerings internos e externos:

* ASNs público: 8074, 8075, 12076
* ASNs particular: 65515, 65517, 65518, 65519, 65520

Não pode especificar estes ASNs para os seus dispositivos VPN no local quando se liga a gateways de VPN do Azure.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Existem outros ASNs que não posso utilizar?
Sim, os seguintes ASNs estão [reservados pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) e não podem ser configurados no Gateway de VPN do Azure:

23456, 64496-64511, 65535-65551 e 429496729

### <a name="what-private-asns-can-i-use"></a>Que ASNs privado posso usar?
O intervalo utilizável de ASNs privado que podem ser usados são:

* 64512-65514, 65521-65534

Esses ASNs não são reservados pelo IANA ou pelo Azure para uso e, portanto, podem ser usados para atribuir ao gateway de VPN do Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Posso utilizar o mesmo ASN para as redes VPN no local e as VNets do Azure?
Não, tem de atribuir ASNs diferentes entre as suas redes no local e as VNets do Azure se estiver a ligá-las entre si com o BGP. Os Gateways de VPN do Azure têm uma predefinição ASN de 65515 atribuída,quer o BGP esteja ou não ativado para a conectividade em vários locais. Pode substituir esta predefinição atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois do gateway ter sido criado. Terá de atribuir os seus ASNs no local aos Gateways de Rede Local do Azure correspondentes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Que prefixos de endereços irão os gateways de VPN do Azure anunciar-me?
O gateway de VPN do Azure irá anunciar as seguintes rotas para os seus dispositivos BGP no local:

* Os seus prefixos de endereços VNet
* Prefixos de endereços para cada Gateway de Rede Local ligado ao gateway de VPN do Azure
* Rotas aprendidas de outras sessões de peering de BGP ligadas ao gateway de VPN do Azure, **exceto a rota ou as rotas predefinidas que se sobreponham a um prefixo VNet**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quantos prefixos posso anunciar no gateway de VPN do Azure?
Damos suporte a até 4000 prefixos. A sessão de BGP é ignorada se o número de prefixos exceder o limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota predefinida (0.0.0.0/0) para gateways de VPN do Azure?
Sim.

Tenha em atenção de que esta ação irá forçar todo o tráfego de saída VNet no seu site no local e irá impedir que as VMs de VNet aceitem comunicação pública na Internet diretamente, essas RDP ou SSH da Internet para as VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Posso anunciar os prefixos exatos como prefixos da minha Rede Virtual?

Não, o ato de anunciar os mesmos prefixos como prefixos de endereços da sua Rede Virtual será bloqueado ou filtrado pela plataforma do Azure. No entanto, pode anunciar um prefixo que é um conjunto mais amplo do que tem no interior da Rede Virtual. 

Por exemplo, se a sua rede virtual utilizou o espaço de endereços 10.0.0.0/16, pode anunciar 10.0.0.0/8. Contudo, não pode anunciar 10.0.0.0/16 ou 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Posso utilizar o BGP com as minhas ligações VNet a VNet?
Sim, pode utilizar o BGP para ligações em vários locais e ligações VNet a VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar ligações BGP com ligações não BGP para os meus gateways de VPN do Azure?
Sim, pode misturar ligações BGP e não BGP para o mesmo gateway de VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>O gateway de VPN do Azure suporta encaminhamento de trânsito BGP?
Sim, o encaminhamento de trânsito BGP é suportado. No entanto, os gateways de VPN do Azure **NÃO** anunciarão rotas predefinidas a outros elementos de rede BGP. Para ativar o encaminhamento de trânsito através de vários gateways de VPN do Azure, tem de ativar o BGP em todas as ligações VNet para VNet intermédias. Para obter mais informações, consulte [sobre o BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais do que um túnel entre o gateway de VPN do Azure e a minha rede no local?
Sim, pode estabelecer mais do que um túnel VPN S2S entre um gateway de VPN do Azure e a sua rede no local. Tenha em atenção que todos estes túneis serão contados tendo em conta o número total de túneis dos seus gateways de VPN do Azure e tem de ativar o BGP em ambos os túneis.

Por exemplo, se tiver dois túneis redundantes entre o gateway de VPN do Azure e uma das redes no local, irá consumir dois túneis da quota total para o seu gateway de VPN do Azure (10 para Standard e 30 para HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Posso ter vários túneis entre duas VNets do Azure com BGP?
Sim, mas pelo menos um dos gateways de rede virtual tem de estar na configuração de ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Posso utilizar o BGP para VPN S2S numa configuração de coexistência ExpressRoute/VPN S2S?
Sim. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Que endereço utiliza o gateway de VPN do Azure para o IP do Elemento de Rede BGP?
O gateway de VPN do Azure alocará um único endereço IP do intervalo de GatewaySubnet para gateways de VPN ativo-em espera ou dois endereços IP para gateways de VPN ativos-ativos. Você pode obter os endereços IP (es) BGP reais alocados usando o PowerShell (Get-AzVirtualNetworkGateway, procurar a propriedade "bgpPeeringAddress") ou na portal do Azure (na propriedade "configurar o ASN do BGP" na página configuração do gateway).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos dos endereços IP do Elemento de Rede BGP no meu dispositivo VPN?
Seu endereço de par de BGP local **não deve** ser o mesmo que o endereço IP público do seu dispositivo VPN ou o espaço de endereço de vnet do gateway de VPN. Utilize um endereço IP diferente no dispositivo VPN do IP do Elemento de Rede BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo, mas note que não pode ser um endereço APIPA (169.254.x.x). Especifique este endereço no Gateway de Rede Local correspondente que representa a localização.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como prefixos de endereços para o Gateway de Rede Local ao utilizar o BGP?
O Gateway de Rede Local do Azure especifica os prefixos de endereços iniciais da rede no local. Com o BGP, tem de alocar o prefixo de anfitrião (prefixo /32) do seu endereço IP do Elemento de Rede BGP como o espaço de endereços para essa rede no local. Se o IP do Elemento de Rede BGP for 10.52.255.254, deve especificar “10.52.255.254/32” como o localNetworkAddressSpace do Gateway de Rede Local que representa esta rede no local. Trata-se de garantir que o gateway de VPN do Azure estabelece a sessão BGP através do túnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar ao meu dispositivo VPN no local para a sessão de peering de BGP?
Deve adicionar uma rota de anfitrião do endereço IP do Elemento de Rede BGP do Azure no dispositivo VPN para apontar para o túnel VPN S2S IPsec. Por exemplo, se o IP do Elemento VPN do Azure for “10.12.255.30”, deve adicionar no dispositivo VPN uma rota de anfitrião para “10.12.255.30” com uma interface nexthop da interface de túnel IPsec correspondente.
