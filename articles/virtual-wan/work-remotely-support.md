---
title: Wan Virtual Azure e trabalhando remotamente
description: Esta página descreve como pode aproveitar o Azure Virtual WAN para permitir o trabalho remotamente devido à pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337133"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Wan Virtual Azure e apoio ao trabalho remoto

>[!NOTE]
>Este artigo descreve como pode alavancar o Azure Virtual WAN, o Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Está a esforçar-se para fornecer conectividade a utilizadores remotos?
De repente vê a necessidade de suportar uma onda de utilizadores para além do que tinha planeado?
Precisa que o utilizador se conecte a partir de casa e não só aceda à nuvem, como também possa chegar ao local?
Precisa que os seus utilizadores remotos consigam alcançar recursos por trás de um WAN privado?
Tem necessidade de os utilizadores acederem a recursos intra-cloud sem terem a necessidade de estabelecer conectividade entre regiões?
À medida que esta pandemia global cria mudanças sem precedentes à nossa volta, a equipa Azure Virtual WAN está aqui para satisfazer as suas necessidades de conectividade.

O Azure Virtual WAN é um serviço de networking que reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Estas funcionalidades incluem conectividade de ramificação (via automatização de conectividade de dispositivos Virtual WAN Partner, tais como SD-WAN ou VPN CPE), conectividade VPN site-to-site, conectividade VPN (Ponto-a-local), Conectividade Privada (ExpressRoute), Conectividade intra cloud (conectividade transitiva para redes virtuais), VPN ExpressRoute Interconectividade, Routing, firewall Azure, Encriptação para conectividade privada etc. Não é preciso ter todos estes casos de uso para começar a usar WAN virtual. Pode começar com apenas uma caixa de utilização e ajustar a sua rede à medida que evolui.

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Agora, falando de utilizadores remotos, vamos ver o que precisa para pôr a sua rede a funcionar:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configurar a conectividade remota do utilizador

Pode ligar-se aos seus recursos em Azure através de uma ligação IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação requer que um cliente VPN seja configurado para o utilizador remoto. Este cliente pode ser o [cliente Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554) ou O Cliente OpenVPN ou qualquer cliente que suporte o IKEv2. Para mais informações, consulte [Criar uma ligação ponto-a-local](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Conectividade do utilizador remoto para o local

Tem duas opções aqui:

* Configurar a conectividade Site-to-site com qualquer dispositivo VPN existente. Quando liga o dispositivo VPN IPsec ao hub Bluee Virtual WAN, a interconectividade entre o Utilizador Ponto-a-site VPN (utilizador remoto) e VPN site-to-site é automática. Para obter mais informações sobre como configurar vpN site-to-site a partir do seu dispositivo VPN no local para Azure Virtual WAN, consulte [Criar uma ligação site-to-site usando o Virtual WAN](virtual-wan-site-to-site-portal.md).

* Ligue o circuito ExpressRoute ao centro Virtual WAN. Ligar um circuito ExpressRoute requer a implementação de um gateway ExpressRoute em Wan Virtual. Assim que tiver implementado um, a interconectividade entre o utilizador do Ponto para o local VPN e o utilizador ExpressRoute é automática. Para criar a ligação ExpressRoute, consulte [Criar uma ligação ExpressRoute utilizando o WAN Virtual](virtual-wan-expressroute-portal.md). Pode utilizar um circuito ExpressRoute existente para ligar ao Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente básico de WAN virtual existente

Basic Virtual WAN fornece apenas VPN site-to-site. Para que os utilizadores remotos se conectem, terá de atualizar o WAN virtual para o Standard Virtual WAN. Para passos para atualizar um WAN virtual, consulte [Upgrade um WAN virtual de Basic para Standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Informações adicionais

O WAN virtual suporta um centro por região/localização. Para obter informações sobre a localização, consulte o artigo dos [parceiros e locais virtuais WAN.](virtual-wan-locations-partners.md) Cada hub suporta até 10.000 ligações remotas de utilizadores, 1.000 ligações de ramificação, quatro circuitos ExpressRoute e até 500 ligações de Rede Virtual. À medida que escala os utilizadores remotos, se tiver alguma dúvida, não azurevirtualwan@microsoft.comhesite em procurar ajuda enviando um e-mail para . Se necessitar de apoio técnico, certifique-se de abrir um bilhete de apoio do portal Azure e a ajuda estará a caminho.

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre o WAN Virtual, consulte [a visão geral do WAN virtual](virtual-wan-about.md)