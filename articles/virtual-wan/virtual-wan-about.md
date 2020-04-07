---
title: Descrição geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a conectividade automática de ramo-a-filial virtual WAN, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743100"
---
# <a name="about-azure-virtual-wan"></a>Sobre o Azure Virtual WAN

O Azure Virtual WAN é um serviço de networking que reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Estas funcionalidades incluem conectividade de ramificação (via automatização de conectividade de dispositivos Virtual WAN Partner, como SD-WAN ou VPN CPE) , conectividade VPN site-to-site, conectividade VPN (Ponto-a-local), Conectividade Privada (ExpressRoute), Conectividade intra-nuvem (conectividade transitiva para redes virtuais), VPN ExpressRoute Interconectividade, Routing, firewall Azure, Encriptação para conectividade privada etc. Não é preciso ter todos estes casos de utilização para começar a usar O WAN Virtual. Você pode simplesmente começar com apenas um caso de uso e ajustar a sua rede à medida que evolui. A arquitetura Virtual WAN é um hub e arquitetura falada com escala e desempenho incorporados para sucursais (dispositivos VPN/SD-WAN), utilizadores (Clientes Azure VPN/OpenVPN/IKEv2), circuitos ExpressRoute e Redes Virtuais. Permite a arquitetura global da rede de [trânsito](virtual-wan-global-transit-network-architecture.md) onde o 'hub' da rede hospedada em nuvem permite uma conectividade transitiva entre pontos finais que podem ser distribuídos por diferentes tipos de 'raios'.

As regiões azure servem como centros aos quais pode optar por se ligar. Todos os hubs estão ligados em malha completa num Wan Virtual Standard, facilitando ao utilizador a utilização da espinha dorsal da Microsoft para qualquer (qualquer)conectividade (qualquer falado). Para a conectividade com os dispositivos SD-WAN/VPN, os utilizadores podem instalá-lo manualmente no Azure Virtual WAN ou utilizar a solução de parceiro virtual WAN CPE (SD-WAN/VPN) para estabelecer conectividade com o Azure. Dispomos de uma lista de parceiros que suportam a automatização da conectividade (capacidade de exportar a informação do dispositivo para o Azure, descarregar a configuração Do Azure e estabelecer conectividade) com o Azure Virtual WAN. Para mais informações, consulte o artigo dos [parceiros e locais virtuais WAN.](virtual-wan-locations-partners.md) 

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma visão rápida da conectividade da rede em Azure Virtual WAN. A WAN Virtual oferece as seguintes vantagens:

* **Soluções integradas de conectividade no hub e faladas:** Automatizar a configuração local-a-local e a conectividade entre os locais no local e um hub Azure.
* **Instalação e configuração automatizadas do spoke:** ligue as redes virtuais e as cargas de trabalho diretamente ao hub do Azure.
* **Resolução intuitiva de problemas:** Você pode ver o fluxo de ponta a ponta dentro do Azure, e depois usar esta informação para tomar as ações necessárias.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>WaNs virtuais básicos e standard

Existem dois tipos de WANs virtuais: Básico e Standard. A tabela seguinte mostra as configurações disponíveis para cada tipo.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Para obter passos para atualizar um WAN virtual, consulte [Atualização de um WAN virtual de Basic para Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Para obter informações sobre a arquitetura Virtual WAN e como migrar para o WAN Virtual, consulte os seguintes artigos:

* [Arquitetura virtual WAN](migrate-from-hub-spoke-topology.md)
* [Arquitetura de rede de trânsito global](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Recursos da WAN virtual

Para configurar uma WAN virtual ponto a ponto, crie os seguintes recursos:

* **virtualWAN:** o recurso virtualWAN representa uma sobreposição virtual da rede do Azure e é uma coleção de vários recursos. Contém ligações a todos os hubs virtuais que quer ter na WAN virtual. Os recursos da WAN virtual estão isolados uns dos outros e não podem conter um hub comum. Os centros virtuais em toda a WAN virtual não comunicam uns com os outros.

* **Hub:** um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para permitir a conectividade. A partir da sua rede no local (vpnsite), pode ligar-se a um Gateway VPN dentro do centro virtual, ligar circuitos ExpressRoute a um hub virtual, ou até ligar utilizadores móveis a um gateway ponto-a-site no centro virtual. O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure.

  Um gateway do hub não é igual a um gateway de rede virtual que utiliza para o ExpressRoute e o Gateway de VPN. Por exemplo, ao utilizar o Virtual WAN, não cria uma ligação site-a-site do seu site no local diretamente para o seu VNet. Em vez disso, cria-se uma ligação site-a-local com o centro. O tráfego passa sempre pelo gateway do hub. Isto significa que as VNets não precisam do seu próprio gateway de rede virtual. A WAN Virtual permite às VNets tirar partido do fácil dimensionamento através do hub virtual e do gateway do hub virtual.

* **Ligação de rede virtual do hub:** o recurso de ligação da rede virtual do hub é utilizado para ligar o hub diretamente à sua rede virtual.

* **(Pré-visualização) Ligação Hub-to-Hub** - Os hubs estão todos ligados uns aos outros num WAN virtual. Isto implica que um ramo, utilizador ou VNet ligado a um hub local pode comunicar com outro ramo ou VNet usando a arquitetura de malha completa dos centros conectados. Também pode ligar VNets dentro de um hub que transita através do centro virtual, bem como VNets através do hub, usando a estrutura ligada ao hub-to-hub.

* **Tabela de rotas do hub:** pode criar uma rota do hub virtual e aplicar a rota à tabela de rotas do hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN virtual**

  * **Local:** Este recurso é utilizado apenas para ligações site-to-site. O recurso do site é **vpnsite**. Representa o seu dispositivo VPN no local e as suas definições. Ao trabalhar com um parceiro de WAN Virtual, tem uma solução integrada para exportar automaticamente estas informações para o Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Tipos de conectividade

O WAN virtual permite os seguintes tipos de conectividade: VPN site-to-site, User VPN (Ponto-a-Local) e ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Ligações de Rede de VPNs

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan.png)

Quando criar uma ligação virtual wan site-to-site, você pode trabalhar com um parceiro disponível. Se não quiser utilizar um parceiro, pode configurar a ligação manualmente. Para mais informações, consulte [Criar uma ligação site-a-site utilizando o WAN virtual](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Fluxo de trabalho de parceiro virtual WAN

Quando se trabalha com um parceiro Virtual WAN, o fluxo de trabalho é:

1. O controlador do dispositivo de ramificação (VPN/SDWAN) é autenticado para exportar informações centradas no local para o Azure utilizando um diretor de [serviço seleção Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. O controlador de dispositivos de ramo (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isto automatiza a transferência de configuração, a edição e a atualização do dispositivo VPN no local.
3. Depois de o dispositivo ter a configuração certa do Azure, é estabelecida uma ligação site a site (dois túneis ativos) à WAN do Azure. O Azure suporta IKEv1 e IKEv2. O BGP é opcional.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Parceiros para ligações wan virtuais site-to-site

Para obter uma lista dos parceiros e locais disponíveis, consulte o artigo de [parceiros e locais Virtuais WAN.](virtual-wan-locations-partners.md)

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Ligações VPN do utilizador (ponto-a-local)

Pode ligar-se aos seus recursos em Azure através de uma ligação IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação requer que um cliente VPN seja configurado no computador cliente. Para mais informações, consulte [Criar uma ligação ponto-a-local](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Ligações ExpressRoute
O ExpressRoute permite ligar a rede no local ao Azure sobre uma ligação privada. Para criar a ligação, consulte [Criar uma ligação ExpressRoute utilizando o WAN Virtual](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Localizações

Para obter informações sobre a localização, consulte o artigo dos [parceiros e locais virtuais WAN.](virtual-wan-locations-partners.md)

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes

[Criar uma ligação site-a-site usando o WAN virtual](virtual-wan-site-to-site-portal.md)
