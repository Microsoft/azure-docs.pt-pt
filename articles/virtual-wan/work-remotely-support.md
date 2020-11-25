---
title: Azure Virtual WAN e trabalhando remotamente
description: Esta página descreve como pode aproveitar a Azure Virtual WAN para ativar o funcionamento remotamente devido à pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023500"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN e apoio ao trabalho remoto

>[!NOTE]
>Este artigo descreve como pode aproveitar a Azure Virtual WAN, Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Está a esforçar-se para fornecer conectividade aos utilizadores remotos?
De repente, vê a necessidade de suportar uma onda de utilizadores para além do que tinha planeado?
Precisa que o utilizador se conecte a partir de casa e não só aceda à nuvem, mas também seja capaz de chegar ao local?
Precisa que os seus utilizadores remotos consigam obter recursos por detrás de um WAN privado?
Tem necessidade de os utilizadores acederem a recursos intra-nuvem sem necessidade de estabelecer conectividade entre regiões?
À medida que esta pandemia global cria mudanças sem precedentes à nossa volta, a equipa Azure Virtual WAN está aqui para atender às suas necessidades de conectividade.

Azure Virtual WAN é um serviço de networking que reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Estas funcionalidades incluem conectividade branch (através da automatização de conectividade a partir de dispositivos Parceiros WAN Virtuais, tais como SD-WAN ou VPN CPE), conectividade VPN site-to-site, conectividade VPN do utilizador remoto (ponto a local), conectividade privada (ExpressRoute), conectividade intra-nuvem (conectividade transitiva para redes virtuais), interconectividade VPN ExpressRoute, Rout, Firewall Azure, Encriptação para conectividade privada etc. Não é preciso ter todos estes casos de uso para começar a usar WAN Virtual. Pode começar com apenas uma caixa de uso e ajustar a sua rede à medida que evolui.

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwan1.png)

Agora, falando de utilizadores remotos, vamos ver o que precisa para pôr a sua rede a funcionar:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configurar a conectividade remota do utilizador

Pode ligar-se aos seus recursos em Azure sobre uma ligação IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação requer que um cliente VPN seja configurado para o utilizador remoto. Este cliente pode ser o [cliente Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554) ou Cliente OpenVPN ou qualquer cliente que suporte iKEv2. Para obter mais informações, consulte [Criar uma ligação ponto-a-local](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Conectividade do utilizador remoto para as instalações

Tem duas opções aqui:

* Configurar a conectividade site-to-site com qualquer dispositivo VPN existente. Quando liga o dispositivo VPN IPsec ao hub Azure Virtual WAN, a interconectividade entre o VPN do utilizador ponto-a-local (utilizador remoto) e o VPN local-para-local é automático. Para obter mais informações sobre como configurar a VPN site-to-site do seu dispositivo VPN no local para Azure Virtual WAN, consulte [Criar uma ligação site-to-site utilizando O WAN Virtual](virtual-wan-site-to-site-portal.md).

* Ligue o seu circuito ExpressRoute ao centro Virtual WAN. A ligação de um circuito ExpressRoute requer a implantação de uma porta de entrada ExpressRoute em VIRTUAL WAN. Assim que tiver implementado uma, a interconectividade entre o utilizador VPN e o utilizador ExpressRoute é automática. Para criar a ligação ExpressRoute, consulte [Criar uma ligação ExpressRoute utilizando o VIRTUAL WAN](virtual-wan-expressroute-portal.md). Pode utilizar um circuito ExpressRoute existente para ligar ao Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente virtual WAN básico existente

O BASIC Virtual WAN fornece apenas VPN site-to-site. Para que os utilizadores remotos se conectem, terá de atualizar o WAN virtual para o WAN virtual padrão. Para obter passos para atualizar um WAN virtual, consulte [upgrade de um WAN virtual de Basic para Standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Informação adicional

O WAN virtual suporta um hub por região/localização. Para obter informações sobre a localização, consulte os [parceiros e locais do Virtual WAN.](virtual-wan-locations-partners.md) Cada hub suporta até 10.000 ligações remotas de utilizador, 1.000 ligações de filial, quatro circuitos ExpressRoute e até 500 ligações de Rede Virtual. À medida que aumenta os utilizadores remotos, se tiver alguma dúvida, não hesite em procurar ajuda enviando um e-mail para azurevirtualwan@microsoft.com . Se necessitar de apoio técnico, certifique-se de abrir um bilhete de apoio a partir do portal Azure e a ajuda estará a caminho.

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre o VIRTUAL WAN, consulte [a visão geral do WAN virtual](virtual-wan-about.md)