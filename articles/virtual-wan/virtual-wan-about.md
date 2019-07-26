---
title: Descrição geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a conectividade de Branch para ramificação de WAN virtual automatizada, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406342"
---
# <a name="what-is-azure-virtual-wan"></a>O que é a WAN Virtual do Azure?

A WAN virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para o, e por meio do Azure. As regiões do Azure servem como hubs para os quais você pode escolher conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar ramificações e aproveitar a conectividade de ramificação para VNet. Temos uma lista de parceiros que dão suporte à automação de conectividade com VPN de WAN virtual do Azure. Para obter mais informações, consulte o artigo [parceiros e locais de WAN virtual](virtual-wan-locations-partners.md) .

A WAN virtual do Azure reúne muitos serviços de conectividade em nuvem do Azure, como VPN site a site e ExpressRoute em uma única interface operacional. A conectividade com o Azure VNets é estabelecida usando conexões de rede virtual.

O ExpressRoute para WAN virtual está atualmente em versão prévia.

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma visão geral da conectividade de rede na WAN virtual do Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integradas no Hub e spoke:** Automatize a configuração e a conectividade site a site entre sites locais e um Hub do Azure.
* **Configuração e instalação automatizada do spoke:** Conecte suas cargas de trabalho e redes virtuais ao Hub do Azure diretamente.
* **Solução de problemas intuitiva:** Você pode ver o fluxo de ponta a ponta no Azure e, em seguida, usar essas informações para executar as ações necessárias.

## <a name="resources"></a>Recursos da WAN virtual

Para configurar uma WAN virtual de ponta a ponta, você cria os seguintes recursos:

* **virtualWAN:** O recurso virtualWAN representa uma sobreposição virtual de sua rede do Azure e é uma coleção de vários recursos. Contém ligações a todos os hubs virtuais que quer ter na WAN virtual. Os recursos da WAN virtual estão isolados uns dos outros e não podem conter um hub comum. Os Hubs Virtuais na WAN Virtual não comunicam entre si. A propriedade ' permitir Branch para o tráfego de ramificações ' permite o tráfego entre sites VPN, bem como VPN para o ExpressRoute (atualmente em visualização) sites habilitados.

* **82801ER** Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). O hub é o núcleo da sua rede numa região. Só pode existir um hub por região do Azure. Quando cria um hub com o portal do Azure, este cria uma VNet e um vpngateway do hub virtual.

  Um gateway do hub não é igual a um gateway de rede virtual que utiliza para o ExpressRoute e o Gateway de VPN. Por exemplo, ao usar a WAN virtual, você não cria uma conexão site a site a partir de seu site local diretamente para sua VNet. Em vez disso, você cria uma conexão site a site com o Hub. O tráfego passa sempre pelo gateway do hub. Isto significa que as VNets não precisam do seu próprio gateway de rede virtual. A WAN Virtual permite às VNets tirar partido do fácil dimensionamento através do hub virtual e do gateway do hub virtual.

* **Conexão de rede virtual do Hub:** O recurso de conexão de rede virtual do hub é usado para conectar o Hub diretamente à sua rede virtual. Neste momento, só pode ligar a redes virtuais que estejam na mesma região do hub.

* **Tabela de rotas do Hub:**  Você pode criar uma rota de Hub virtual e aplicar a rota à tabela de rotas do Hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN virtual**

  * **Locais** Este recurso é usado somente para conexões site a site. O recurso do site é **vpnsite**. Ele representa seu dispositivo VPN local e suas configurações. Ao trabalhar com um parceiro de WAN Virtual, tem uma solução integrada para exportar automaticamente estas informações para o Azure.

## <a name="connectivity"></a>Conectividade

A WAN virtual permite dois tipos de conectividade: Site a site e ExpressRoute (versão prévia).

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


### <a name="er"></a>Ligações do ExpressRoute (Pré-visualização)

O ExpressRoute permite que você conecte a rede local ao Azure por meio de uma conexão privada. Para criar a conexão, consulte [criar uma conexão de ExpressRoute usando a WAN virtual](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Locais

Para obter informações de localização, consulte o artigo [parceiros e locais de WAN virtual](virtual-wan-locations-partners.md) .

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos Seguintes

[Criar uma conexão site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md)
