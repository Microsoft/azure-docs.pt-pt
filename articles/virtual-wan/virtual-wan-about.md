---
title: Descrição geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a conectividade de Branch para ramificação de WAN virtual automatizada, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 3b33015b3a69372f6783d59b28a111ec279dd860
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048478"
---
# <a name="about-azure-virtual-wan"></a>Sobre a WAN virtual do Azure

A WAN virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para o, e por meio do Azure. As regiões do Azure servem como hubs para os quais você pode escolher conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar ramificações e aproveitar a conectividade de ramificação para VNet. Temos uma lista de parceiros que dão suporte à automação de conectividade com VPN de WAN virtual do Azure. Para obter mais informações, consulte o artigo [parceiros e locais de WAN virtual](virtual-wan-locations-partners.md) .

A WAN virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, VPN de usuário (ponto a site) e ExpressRoute em uma única interface operacional. A conectividade com o Azure VNets é estabelecida usando conexões de rede virtual. Ele habilita a [arquitetura de rede de trânsito global](virtual-wan-global-transit-network-architecture.md) com base em um modelo de conectividade de Hub e spoke clássico em que o ' hub ' da rede hospedada na nuvem permite a conectividade transitiva entre os pontos de extremidade que podem ser distribuídos entre os diferentes tipos de ' spokes '.

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma visão geral da conectividade de rede na WAN virtual do Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integradas no Hub e spoke:** Automatize a configuração e a conectividade site a site entre sites locais e um Hub do Azure.
* **Instalação e configuração automatizadas do spoke:** ligue as redes virtuais e as cargas de trabalho diretamente ao hub do Azure.
* **Solução de problemas intuitiva:** Você pode ver o fluxo de ponta a ponta no Azure e, em seguida, usar essas informações para executar as ações necessárias.

## <a name="basicstandard"></a>WANs virtuais básicas e Standard

Há dois tipos de WANs virtuais: básica e Standard. A tabela a seguir mostra as configurações disponíveis para cada tipo.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Para obter as etapas para atualizar uma WAN virtual, consulte [atualizar uma WAN virtual de básico para Standard](upgrade-virtual-wan.md).

## <a name="resources"></a>Recursos da WAN virtual

Para configurar uma WAN virtual ponto a ponto, crie os seguintes recursos:

* **virtualWAN:** o recurso virtualWAN representa uma sobreposição virtual da rede do Azure e é uma coleção de vários recursos. Contém ligações a todos os hubs virtuais que quer ter na WAN virtual. Os recursos da WAN virtual estão isolados uns dos outros e não podem conter um hub comum. Os hubs virtuais na WAN virtual não se comunicam entre si.

* **Hub:** um hub virtual é uma rede virtual gerida pela Microsoft. O Hub contém vários pontos de extremidade de serviço para habilitar a conectividade. A partir de sua rede local (vpnsite), você pode se conectar a um gateway de VPN dentro do Hub virtual, conectar circuitos do ExpressRoute a um hub virtual ou até mesmo conectar usuários móveis a um gateway ponto a site no Hub virtual. O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure.

  Um gateway do hub não é igual a um gateway de rede virtual que utiliza para o ExpressRoute e o Gateway de VPN. Por exemplo, ao usar a WAN virtual, você não cria uma conexão site a site a partir de seu site local diretamente para sua VNet. Em vez disso, você cria uma conexão site a site com o Hub. O tráfego passa sempre pelo gateway do hub. Isto significa que as VNets não precisam do seu próprio gateway de rede virtual. A WAN Virtual permite às VNets tirar partido do fácil dimensionamento através do hub virtual e do gateway do hub virtual.

* **Ligação de rede virtual do hub:** o recurso de ligação da rede virtual do hub é utilizado para ligar o hub diretamente à sua rede virtual.

* **(Visualização) conexão de Hub para Hub** -os hubs estão todos conectados entre si em uma WAN virtual. Isso implica que uma ramificação, um usuário ou uma VNet conectada a um hub local pode se comunicar com outro Branch ou VNet usando a arquitetura de malha completa dos hubs conectados. Você também pode conectar o VNets em um Hub que passa pelo Hub virtual, bem como VNets ao longo do Hub, usando a estrutura conectada Hub para o Hub.

* **Tabela de rotas do hub:** pode criar uma rota do hub virtual e aplicar a rota à tabela de rotas do hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN virtual**

  * **Site:** Este recurso é usado somente para conexões site a site. O recurso do site é **vpnsite**. Ele representa seu dispositivo VPN local e suas configurações. Ao trabalhar com um parceiro de WAN Virtual, tem uma solução integrada para exportar automaticamente estas informações para o Azure.

## <a name="connectivity"></a>Conectividade

A WAN virtual permite os seguintes tipos de conectividade: VPN site a site, VPN de usuário (ponto a site) e ExpressRoute.

### <a name="s2s"></a>Conexões VPN site a site

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan.png)

Ao criar uma conexão site a site de WAN virtual, você pode trabalhar com um parceiro disponível. Se você não quiser usar um parceiro, poderá configurar a conexão manualmente. Para obter mais informações, consulte [criar uma conexão site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Fluxo de trabalho do parceiro de WAN virtual

Quando você trabalha com um parceiro de WAN virtual, o fluxo de trabalho é:

1. O controlador de dispositivos de ramo (VPN/SDWAN) é autenticado para exportar informações centradas nos sites para o Azure com um [Principal de Serviço do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. O controlador de dispositivos de ramo (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isto automatiza a transferência de configuração, a edição e a atualização do dispositivo VPN no local.
3. Depois de o dispositivo ter a configuração certa do Azure, é estabelecida uma ligação site a site (dois túneis ativos) à WAN do Azure. O Azure suporta IKEv1 e IKEv2. O BGP é opcional.

#### <a name="partners"></a>Parceiros para conexões WAN virtuais site a site

Para obter uma lista dos parceiros e locais disponíveis, consulte o artigo [parceiros e locais de WAN virtual](virtual-wan-locations-partners.md) .

### <a name="uservpn"></a>Conexões VPN de usuário (ponto a site)

Você pode se conectar aos seus recursos no Azure por meio de uma conexão de IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão requer que um cliente VPN seja configurado no computador cliente. Para obter mais informações, consulte [criar uma conexão ponto a site](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Conexões do ExpressRoute
O ExpressRoute permite que você conecte a rede local ao Azure por meio de uma conexão privada. Para criar a conexão, consulte [criar uma conexão de ExpressRoute usando a WAN virtual](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Locais

Para obter informações de localização, consulte o artigo [parceiros e locais de WAN virtual](virtual-wan-locations-partners.md) .

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

[Criar uma conexão site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md)
