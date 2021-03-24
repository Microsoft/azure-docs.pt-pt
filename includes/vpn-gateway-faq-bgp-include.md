---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3877134f8a00cd627909d7f889fd5b104ccbd8b1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863630"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP é suportado em todos os SKUs do VPN Gateway do Azure?
O BGP é suportado em todos os SKUs Azure VPN Gateway, exceto o Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Posso usar o BGP com gateways VPN da Política Azure?
Não, o BGP é suportado apenas em gateways VPN baseados em rotas.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Que ASNs (Números de Sistema Autónomos) posso usar?
Você pode usar as suas próprias ASNs públicas ou ASNs privadas tanto para as suas redes no local como para redes virtuais Azure. Não pode utilizar as gamas reservadas pela Azure ou pela IANA.

As seguintes ASNs são reservadas pela Azure ou pela IANA:
* ASNs reservadas por Azure:
  * Public ASNs: 8074, 8075, 12076
  * ASNs privados: 65515 65517, 65518, 65519, 65520
* ASNs [reservadas pela IANA:](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
   * 23456, 64496-64511, 65535-65551 e 429496729

Não é possível especificar estas ASNs para os seus dispositivos VPN no local quando estiver a ligar-se aos gateways Azure VPN.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Posso usar ASNs de 32 bits (4 bytes).
Sim, o VPN Gateway agora suporta ASNs de 32 bits (4 bytes). Para configurar utilizando a ASN em formato decimal, utilize o PowerShell, o Azure CLI ou o Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Que ASNs privada posso usar?
As gamas utilizáveis de ASNs privadas são:

* 64512-65514 e 65521-65534

Estas ASNs não são reservadas pela IANA ou pela Azure para utilização, pelo que podem ser usadas para atribuir ao seu gateway Azure VPN.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Que endereço utiliza o Gateway VPN para o IP peer peer BGP?

Por predefinição, o VPN Gateway atribui um único endereço IP da gama *GatewaySubnet* para gateways VPN de standby ativo, ou dois endereços IP para gateways VPN ativos. Estes endereços são atribuídos automaticamente quando cria o gateway VPN. Pode obter o endereço IP real do BGP atribuído utilizando o PowerShell ou localizando-o no portal Azure. Em PowerShell, use **Get-AzVirtualNetworkGateway** e procure a propriedade **bgpPeeringAddress.** No portal Azure, na página **de Configuração Gateway,** consulte a propriedade **Configure BGP ASN.**

Se os seus routers VPN no local utilizarem endereços **IP APIPA** (169.254.x.x) como endereços IP BGP, deve especificar um **endereço IP Azure APIPA BGP** adicional no seu gateway Azure VPN. A Azure VPN Gateway seleciona o endereço APIPA para utilizar com o par APIPA BGP no local especificado no gateway de rede local, ou o endereço IP privado para um par BGP não APIPA, no local. Para mais informações, consulte [o Configure BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para os endereços IP peer BGP no meu dispositivo VPN?
O seu endereço de pares BGP no local não deve ser o mesmo que o endereço IP público do seu dispositivo VPN ou do espaço de endereço de rede virtual do gateway VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de pares BGP. Pode ser um endereço atribuído à interface loopback no dispositivo (seja um endereço IP regular ou um endereço APIPA). Se o seu dispositivo utilizar um endereço APIPA para BGP, tem de especificar um endereço IP APIPA BGP no seu gateway Azure VPN, conforme descrito no [Configure BGP](../articles/vpn-gateway/bgp-howto.md). Especifique este endereço no portal de rede local correspondente que representa a localização.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como prefixos de endereço para o gateway de rede local quando uso BGP?

> [!IMPORTANT]
>
>Trata-se de uma alteração da exigência previamente documentada. Se utilizar o BGP para uma ligação, deixe o campo **de espaço Address** vazio para o recurso de gateway de rede local correspondente. A Azure VPN Gateway adiciona uma rota de anfitrião internamente ao IP de pares BGP no local sobre o túnel IPsec. Não adicione a rota /32 no campo **espacial Address.** É redundante e se usar um endereço APIPA como o dispositivo VPN no local, O IP BGP, não pode ser adicionado a este campo. Se adicionar quaisquer outros prefixos no campo **espacial Address,** são adicionados como rotas estáticas no gateway Azure VPN, além das rotas aprendidas via BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Posso utilizar a mesma ASN tanto para as redes VPN no local como para as redes virtuais Azure?
Não, deve atribuir diferentes ASNs entre as suas redes no local e as suas redes virtuais Azure se estiver a conectá-las com o BGP. Os gateways Azure VPN têm uma ASN padrão de 65515 atribuída, quer o BGP esteja ativado ou não para a sua conectividade de instalações cruzadas. Pode anular este padrão atribuindo uma ASN diferente quando estiver a criar o gateway VPN, ou pode alterar a ASN após a criação do gateway. Terá de atribuir as suas ASNs no local às respetivas portas de rede locais do Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Que prefixos de endereços irão os gateways de VPN do Azure anunciar-me?
Os gateways anunciam as seguintes rotas para os seus dispositivos BGP no local:

* Os pré-prefixos do seu endereço de rede virtual.
* Prefixos de endereço para cada porta de entrada de rede local ligado ao gateway Azure VPN.
* Rotas aprendidas a partir de outras sessões de observação BGP ligadas ao gateway Azure VPN, exceto para a rota padrão ou rotas que se sobrepõem a qualquer prefixo de rede virtual.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quantos prefixos posso anunciar ao Azure VPN Gateway?
O Azure VPN Gateway suporta até 4000 prefixos. A sessão de BGP é ignorada se o número de prefixos exceder o limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota predefinida (0.0.0.0/0) para gateways de VPN do Azure?
Sim. Note que isto força toda a rede virtual a deslocar tráfego para o seu local. Também impede que os VMs de rede virtual aceitem a comunicação pública diretamente da internet, tais como RDP ou SSH da internet para os VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Posso anunciar os prefixos exatos como os meus prefixos de rede virtuais?

Não, a publicidade dos mesmos prefixos que qualquer um dos seus prefixos de endereço de rede virtual será bloqueada ou filtrada pelo Azure. Pode, no entanto, anunciar um prefixo que é um superconjunto do que tem dentro da sua rede virtual. 

Por exemplo, se a sua rede virtual usou o espaço de endereço 10.0.0.0/16, pode anunciar 10.0.0.0/8. Mas não pode anunciar 10.0.0.0/16 ou 10.0.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Posso utilizar o BGP com as minhas ligações entre redes virtuais?
Sim, pode utilizar o BGP para ligações cruzadas e ligações entre redes virtuais.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar ligações BGP com ligações não BGP para os meus gateways de VPN do Azure?
Sim, pode misturar ligações BGP e não BGP para o mesmo gateway de VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>A Azure VPN Gateway suporta o encaminhamento de trânsito BGP?
Sim, o encaminhamento de trânsito BGP é suportado, com a exceção de que os gateways Azure VPN não anunciam rotas padrão para outros pares BGP. Para permitir o encaminhamento de trânsito através de vários gateways Azure VPN, deve ativar o BGP em todas as ligações intermédias entre redes virtuais. Para mais informações, consulte [Sobre o BGP.](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais do que um túnel entre uma porta VPN Azure e a minha rede no local?
Sim, pode estabelecer mais de um túnel VPN local-a-local (S2S) entre uma porta VPN Azure e a sua rede no local. Note que todos estes túneis são contados contra o número total de túneis para os seus gateways Azure VPN, e você deve ativar BGP em ambos os túneis.

Por exemplo, se tiver dois túneis redundantes entre o seu gateway Azure VPN e uma das suas redes no local, eles consomem 2 túneis fora da quota total para o seu gateway Azure VPN.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Posso ter vários túneis entre duas redes virtuais Azure com BGP?
Sim, mas pelo menos um dos gateways de rede virtual tem de estar na configuração de ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Posso utilizar o BGP para VPN S2S numa configuração de coexistência Azure ExpressRoute e S2S VPN?
Sim. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar ao meu dispositivo VPN no local para a sessão de peering de BGP?
Adicione uma rota de anfitrião do endereço IP peer Azure BGP no seu dispositivo VPN. Esta rota aponta para o túnel VPN IPsec S2S. Por exemplo, se o Azure VPN peer IP for 10.12.255.30, adicione uma rota de anfitrião para 10.12.255.30 com uma interface de próximo lúpulo da interface do túnel IPsec correspondente no seu dispositivo VPN.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>A porta de entrada de rede virtual suporta BFD para ligações S2S com BGP?
N.º O Bidirectional Forwarding Detection (BFD) é um protocolo que pode usar com o BGP para detetar o tempo de inatividade do vizinho mais rapidamente do que pode, utilizando "keepalives" padrão do BGP. O BFD utiliza temporizadores de subsegundos concebidos para trabalhar em ambientes LAN, mas não em toda a internet pública ou ligações de Rede de Vastas Áreas.

Para ligações através da internet pública, ter certos pacotes atrasados ou mesmo largados não é incomum, por isso introduzir estes temporizadores agressivos pode acrescentar instabilidade. Esta instabilidade pode fazer com que as rotas sejam atenuadas pelo BGP. Como alternativa, pode configurar o seu dispositivo no local com temporizadores inferiores ao padrão, intervalo "keepalive" de 60 segundos e o temporizador de 180 segundos. Isto resulta num tempo de convergência mais rápido.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>As portas de VPN da Azure iniciam sessões ou ligações de observação de BGP?

O gateway iniciará sessões de observação BGP para os endereços IP peer peer no local especificados nos recursos de gateway de rede local usando os endereços IP privados nos gateways VPN. Isto não é independente se os endereços IP BGP no local se encontrem na gama APIPA ou endereços IP privados regulares. Se os seus dispositivos VPN no local utilizarem endereços APIPA como IP BGP, é necessário configurar o seu altifalante BGP para iniciar as ligações.

