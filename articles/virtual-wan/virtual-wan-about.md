---
title: Descrição geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a conectividade de ramo-a-ramo automatizado virtual WAN, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 641ed73f507d35d7af548d0164ef8e80979be217
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530481"
---
# <a name="what-is-azure-virtual-wan"></a>O que é a WAN Virtual do Azure?

Azure Virtual WAN é um serviço de networking que reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Estas funcionalidades incluem conectividade de ramo (através da automatização de conectividade a partir de dispositivos Parceiros WAN Virtuais, tais como SD-WAN ou VPN CPE), conectividade VPN site-to-site, conectividade VPN (ponto a local), conectividade privada (ExpressRoute), conectividade intra-nuvem (conectividade transitiva para redes virtuais), interconectividade VPN ExpressRoute, encaminhamento, firewall Azure e encriptação para conectividade privada. Não é preciso ter todos estes casos de utilização para começar a usar WAN Virtual. Pode simplesmente começar com apenas um caso de uso e, em seguida, ajustar a sua rede à medida que evolui.

A arquitetura Virtual WAN é um hub e arquitetura falada com escala e performance incorporada para ramos (dispositivos VPN/SD-WAN), utilizadores (clientes Azure VPN/OpenVPN/IKEv2), circuitos ExpressRoute e redes virtuais. Permite a [arquitetura global da rede de trânsito,](virtual-wan-global-transit-network-architecture.md)onde a rede de internet hospedada em nuvem 'hub' permite a conectividade transitiva entre pontos finais que podem ser distribuídos por diferentes tipos de 'porta-vozes'.

As regiões de Azure servem como centros aos quais pode optar por se conectar. Todos os hubs estão ligados em malha completa num WAN Virtual Standard, facilitando o uso da espinha dorsal da Microsoft para qualquer conectividade (qualquer um falado). Para a conectividade de raios com dispositivos SD-WAN/VPN, os utilizadores podem configurar manualmente em Azure Virtual WAN ou utilizar a solução parceira Virtual WAN CPE (SD-WAN/VPN) para configurar a conectividade com o Azure. Dispomos de uma lista de parceiros que suportam a automatização da conectividade (capacidade de exportar a informação do dispositivo para o Azure, descarregar a configuração Azure e estabelecer conectividade) com a Azure Virtual WAN. Para mais informações, consulte os [parceiros e locais do Virtual WAN.](virtual-wan-locations-partners.md)

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma visão rápida da conectividade da rede em Azure Virtual WAN. A WAN Virtual oferece as seguintes vantagens:

* **Soluções integradas de conectividade no hub e falou:** Automatizar a configuração local-local e a conectividade entre os locais no local e um hub Azure.
* **Instalação e configuração automatizadas do spoke:** ligue as redes virtuais e as cargas de trabalho diretamente ao hub do Azure.
* **Resolução intuitiva de problemas:** Você pode ver o fluxo de ponta a ponta dentro de Azure, e em seguida, usar esta informação para tomar as ações necessárias.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>WANs virtuais básicos e padrão

Existem dois tipos de WANs virtuais: Básico e Standard. A tabela seguinte mostra as configurações disponíveis para cada tipo.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Para obter passos para atualizar um WAN virtual, consulte [upgrade de um WAN virtual de Basic para Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Para obter informações sobre a arquitetura Virtual WAN e como migrar para o WAN Virtual, consulte os seguintes artigos:

* [Arquitetura VIRTUAL WAN](migrate-from-hub-spoke-topology.md)
* [Arquitetura de rede de trânsito global](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Recursos da WAN virtual

Para configurar uma WAN virtual ponto a ponto, crie os seguintes recursos:

* **virtualWAN:** o recurso virtualWAN representa uma sobreposição virtual da rede do Azure e é uma coleção de vários recursos. Contém ligações a todos os hubs virtuais que quer ter na WAN virtual. Os recursos da WAN virtual estão isolados uns dos outros e não podem conter um hub comum. Os centros virtuais em toda a WAN Virtual não comunicam uns com os outros.

* **Hub:** um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para permitir a conectividade. A partir da sua rede no local (vpnsite), pode ligar-se a um Gateway VPN dentro do hub virtual, ligar circuitos ExpressRoute a um hub virtual ou até mesmo ligar os utilizadores móveis a uma porta de entrada point-to-site no centro virtual. O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure.

  Um gateway do hub não é igual a um gateway de rede virtual que utiliza para o ExpressRoute e o Gateway de VPN. Por exemplo, ao utilizar o VIRTUAL WAN, não cria uma ligação site-to-site a partir do seu site no local diretamente para o seu VNet. Em vez disso, cria-se uma ligação site-to-site ao hub. O tráfego passa sempre pelo gateway do hub. Isto significa que as VNets não precisam do seu próprio gateway de rede virtual. A WAN Virtual permite às VNets tirar partido do fácil dimensionamento através do hub virtual e do gateway do hub virtual.

* **Ligação de rede virtual do hub:** o recurso de ligação da rede virtual do hub é utilizado para ligar o hub diretamente à sua rede virtual.

* **Ligação hub-to-hub:** Os centros estão todos ligados uns aos outros num WAN virtual. Isto implica que um ramo, utilizador ou VNet ligado a um hub local pode comunicar com outro ramo ou VNet usando a arquitetura de malha completa dos centros conectados. Também pode ligar VNets dentro de um hub que atravessa o hub virtual, bem como VNets através do hub, usando a estrutura ligada ao hub-a-hub.

* **Tabela de rotas do hub:** pode criar uma rota do hub virtual e aplicar a rota à tabela de rotas do hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN virtuais**

* **Site:** Este recurso é utilizado apenas para ligações locais. O recurso do site é **vpnsite.** Representa o seu dispositivo VPN no local e as suas definições. Ao trabalhar com um parceiro de WAN Virtual, tem uma solução integrada para exportar automaticamente estas informações para o Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Tipos de conectividade

O WAN virtual permite os seguintes tipos de conectividade: VPN site-to-site, VPN do utilizador (Ponto a Local) e ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Ligações de Rede de VPNs

Pode ligar-se aos seus recursos em Azure sobre uma ligação IPsec/IKE (IKEv2) do Site-to-site. Para obter mais informações, consulte [Criar uma ligação site-to-site utilizando o VIRTUAL WAN](virtual-wan-site-to-site-portal.md). 

Este tipo de ligação requer um dispositivo VPN ou um dispositivo Parceiro WAN Virtual. Os parceiros VIRTUAIS WAN fornecem automatização para conectividade, que é a capacidade de exportar a informação do dispositivo para o Azure, baixar a configuração Azure e estabelecer conectividade com o hub Azure Virtual WAN. Para obter uma lista dos parceiros e locais disponíveis, consulte os [parceiros e locais do Virtual WAN.](virtual-wan-locations-partners.md) Se o seu fornecedor de dispositivos VPN/SD-WAN não estiver listado no link mencionado, então pode simplificar a utilização da instrução passo a passo [Criar uma ligação site-a-local utilizando o WAN virtual](virtual-wan-site-to-site-portal.md) para configurar a ligação.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Ligações VPN (ponto a local) do utilizador

Pode ligar-se aos seus recursos em Azure sobre uma ligação IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação requer que um cliente VPN seja configurado no computador cliente. Para obter mais informações, consulte [Criar uma ligação ponto-a-local](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Ligações ExpressRoute
O ExpressRoute permite-lhe ligar a rede no local ao Azure sobre uma ligação privada. Para criar a ligação, consulte [Criar uma ligação ExpressRoute utilizando o VIRTUAL WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Ligações Hub-to-VNet

Pode ligar uma rede virtual Azure a um hub virtual. Para obter mais informações, consulte [Ligar o seu VNet a um hub](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Conectividade de trânsito

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Conectividade de trânsito entre VNets

O WAN virtual permite a conectividade de trânsito entre VNets. Os VNets ligam-se a um hub virtual através de uma ligação de rede virtual. A conectividade de trânsito entre os VNets em **STANDARD Virtual WAN** está ativada devido à presença de um router em todos os centros virtuais. Este router é instantâneo quando o hub virtual é criado pela primeira vez.

O router pode ter quatro estatutos de encaminhamento: Provisioned, Provisioning, Failed, ou None. O **estado de encaminhamento** está localizado no portal Azure navegando para a página Virtual Hub.

* A **Nenhum** estado indica que o hub Virtual não foriu o router. Isto pode acontecer se o WAN Virtual for do tipo *Basic*, ou se o hub virtual foi implantado antes do serviço ser disponibilizado.
* Um **estado falhado** indica falha durante a instantânea. Para instantaneaizar ou redefinir o router, pode localizar a opção **Reset Router** navegando na página geral do hub virtual no portal Azure.

Cada router de hub virtual suporta uma produção agregada até 50 Gbps. A conectividade entre as ligações de rede virtual pressupõe um total de 2000 VM de carga de trabalho em todos os VNets ligados a um único Hub virtual.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Conectividade de trânsito entre VPN e ExpressRoute

A WAN virtual permite a conectividade de trânsito entre a VPN e a ExpressRoute. Isto implica que sites ligados a VPN ou utilizadores remotos podem comunicar com sites ligados ao ExpressRoute. Existe também um pressuposto implícito de que a **bandeira branch-to-branch** está ativada e o BGP é suportado nas ligações VPN e ExpressRoute. Esta bandeira pode ser localizada nas definições Azure Virtual WAN no portal Azure. Toda a gestão de rotas é fornecida pelo router de hub virtual, que também permite a conectividade de trânsito entre redes virtuais.

### <a name="custom-routing"></a><a name="routing"></a>Encaminhamento personalizado

O WAN virtual fornece melhorias avançadas de encaminhamento. Capacidade de configurar tabelas de rotas personalizadas, otimizar o encaminhamento de rede virtual com associação de rotas e propagação, logicamente agrupar tabelas de rotas com etiquetas e simplificar inúmeros cenários de encaminhamento de aparelhos virtuais de rede (NVA) ou de encaminhamento de serviços partilhados.

### <a name="global-vnet-peering"></a><a name="global"></a>Peering VNet global

O VNet Peering global fornece um mecanismo para ligar dois VNets em diferentes regiões. No VIRTUAL WAN, as ligações de rede virtuais ligam VNets a centros virtuais. O utilizador não precisa de configurar o VNet global a espreitar explicitamente. Os VNets ligados ao centro virtual na mesma região incorrem em taxas de observação VNet. Os VNets ligados ao centro virtual numa região diferente incorrem em taxas de observação globais de VNet.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Encriptação de tráfego ExpressRoute

A Azure Virtual WAN fornece a capacidade de encriptar o seu tráfego ExpressRoute. A técnica fornece um trânsito encriptado entre as redes no local e as redes virtuais Azure através do ExpressRoute, sem passar pela internet pública ou usar endereços IP públicos. Para obter mais informações, consulte [o IPsec sobre o ExpressRoute para O WAN Virtual.](vpn-over-expressroute.md)

## <a name="locations"></a><a name="locations"></a>Localizações

Para obter informações sobre a localização, consulte os [parceiros e locais do Virtual WAN.](virtual-wan-locations-partners.md)

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Tabelas de rota para WANs virtuais básicas e padrão

As tabelas de rotas têm agora características de associação e propagação. Uma tabela de rotas pré-existente é uma tabela de rotas que não tem estas funcionalidades. Se tiver rotas pré-existentes no encaminhamento do hub e quiser utilizar as novas capacidades, considere o seguinte:

* **Clientes STANDARD VIRTUAL WAN com rotas pré-existentes no hub virtual**: Se tiver rotas pré-existentes na secção de encaminhamento para o hub no portal Azure, terá de as eliminar primeiro e, em seguida, tentar criar novas tabelas de rotas (disponíveis na secção Tabelas de Rota para o hub no portal Azure). É altamente encorajado a fazer o passo de eliminação para todos os hubs em um WAN Virtual.

* **Clientes Básicos DE WAN Virtual com rotas pré-existentes no hub virtual**: Se tiver rotas pré-existentes na secção de encaminhamento para o hub no portal Azure, terá de os eliminar primeiro e, em seguida, **atualizar** o seu BASIC Virtual WAN para O WAN Virtual Padrão. Consulte [a Atualização de um WAN virtual de Basic para Standard](upgrade-virtual-wan.md). É altamente encorajado a fazer o passo de eliminação para todos os hubs em um WAN Virtual.

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>O que há de novo?

Subscreva o feed RSS e veja as últimas atualizações de funcionalidades DE WAN virtuais na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN)

## <a name="next-steps"></a>Passos seguintes

[Criar uma ligação site-a-local usando O WAN Virtual](virtual-wan-site-to-site-portal.md)
