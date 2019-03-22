---
title: Descrição geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a conectividade WAN Virtual automatizada de ramificação para ramificação dimensionável, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6dae42dcc8b74b682c7226916482228058db6154
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336257"
---
# <a name="what-is-azure-virtual-wan"></a>O que é a WAN Virtual do Azure?

WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramo automatizada e otimizado para e através do Azure. Regiões do Azure servem como fontes que pode optar por ligar seus ramos para. Assim que os ramos estão ligados, pode aproveitar o backbone do Azure para estabelecer conectividade ramo-para-VNet e o ramo de ramo. Para obter uma lista de parceiros e localizações que suportam Virtual WAN VPN, consulte a [Virtual WAN parceiros e localizações de](virtual-wan-locations-partners.md) artigo.

WAN Virtual do Azure reúne vários serviços de conectividade de cloud do Azure como site-site VPN (disponível em geral), ExpressRoute (pré-visualização), o utilizador de ponto-para-site VPN (pré-visualização) numa única interface operacional. Conectividade para VNets do Azure é estabelecida através de ligações de rede virtual.

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma vista rápida sobre a conectividade de rede na WAN Virtual do Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integrada no hub- and -spoke:** Automatize a configuração de site a site e a conectividade entre sites no local e um hub do Azure.
* **Instalação automatizada do spoke e configuração:** Ligar a redes virtuais e cargas de trabalho para o hub do Azure forma totalmente integrada.
* **Resolução de problemas intuitivo:** Pode ver o fluxo de ponto-a-ponto no Azure e, em seguida, utilize estas informações para tomar as medidas necessárias.

## <a name="resources"></a>Recursos da WAN virtual

Para configurar uma WAN do ponto-a-ponto Virtual, crie os seguintes recursos:

* **virtualWAN:** O recurso de virtualWAN representa uma sobreposição virtual da sua rede do Azure e é uma coleção de vários recursos. Contém ligações a todos os hubs virtuais que quer ter na WAN virtual. Os recursos da WAN virtual estão isolados uns dos outros e não podem conter um hub comum. Os Hubs Virtuais na WAN Virtual não comunicam entre si. A propriedade "Permitir tráfego de ramificação para ramificação" permite que os Sites de tráfego entre os sites VPN, bem como VPN para o ExpressRoute (atualmente em pré-visualização) ativado.

* **Hub:** Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure. Quando cria um hub com o portal do Azure, este cria uma VNet e um vpngateway do hub virtual.

  Um gateway do hub não é igual a um gateway de rede virtual que utiliza para o ExpressRoute e o Gateway de VPN. Por exemplo, ao utilizar o Virtual WAN, não crie uma ligação site a site do site no local diretamente à VNet. Em vez disso, cria uma ligação de site a site para o hub. O tráfego passa sempre pelo gateway do hub. Isto significa que as VNets não precisam do seu próprio gateway de rede virtual. A WAN Virtual permite às VNets tirar partido do fácil dimensionamento através do hub virtual e do gateway do hub virtual.

* **Ligação do hub de rede virtual:** O recurso de ligação de rede virtual de Hub é utilizado para ligar o hub de forma totalmente integrada à sua rede virtual. Neste momento, só pode ligar a redes virtuais que estejam na mesma região do hub.

* **Tabela de rotas de Hub:**  Pode criar uma rota de virtual hub e aplicam-se a rota à tabela de rotas de virtual hub. Pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de Virtual WAN**

  * **Site:** Este recurso é utilizado para apenas ligações de site a site. O recurso de site é **vpnsite**. Ele representa o dispositivo VPN no local e as respetivas definições. Ao trabalhar com um parceiro de WAN Virtual, tem uma solução integrada para exportar automaticamente estas informações para o Azure.

## <a name="connectivity"></a>Conectividade

Virtual WAN permite três tipos de conectividade: site a site, ponto a site (pré-visualização) e ExpressRoute (pré-visualização).

### <a name="s2s"></a>Ligações de VPN de site a site

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan.png)

Quando cria uma ligação site a site Virtual WAN, pode trabalhar com um parceiro disponível. Se não quiser usar um parceiro, pode configurar a ligação manualmente. Para obter mais informações, consulte [criar uma ligação de site a site com Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Fluxo de trabalho do parceiro WAN virtual

Quando trabalha com um parceiro de Virtual WAN, é o fluxo de trabalho:

1. O controlador de dispositivos de ramo (VPN/SDWAN) é autenticado para exportar informações centradas nos sites para o Azure com um [Principal de Serviço do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. O controlador de dispositivos de ramo (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isto automatiza a transferência de configuração, a edição e a atualização do dispositivo VPN no local.
3. Depois de o dispositivo ter a configuração certa do Azure, é estabelecida uma ligação site a site (dois túneis ativos) à WAN do Azure. O Azure suporta IKEv1 e IKEv2. O BGP é opcional.

#### <a name="partners"></a>Parceiros para conexões de Virtual WAN site a site

Para obter uma lista de parceiros disponíveis e localizações, consulte a [Virtual WAN parceiros e localizações de](virtual-wan-locations-partners.md) artigo.

### <a name="p2s"></a>Ligações de VPN ponto a site (pré-visualização)

Uma ligação do ponto a site (P2S) permite-lhe criar uma ligação segura ao seu hub virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar.

Para criar a ligação, veja [criar uma ligação de ponto a site através da WAN Virtual](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Ligações do ExpressRoute (Pré-visualização)

O ExpressRoute permite-lhe ligar a rede no local para o Azure através de uma ligação privada. Para criar a ligação, veja [crie uma ligação de ExpressRoute através de Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>localizações

Para informações de localização, consulte a [Virtual WAN parceiros e localizações de](virtual-wan-locations-partners.md) artigo.

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos Seguintes

[Criar uma ligação de site a site com Virtual WAN](virtual-wan-site-to-site-portal.md)
