---
title: Seleção de caminhos azure em vários links ISP
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre a seleção de caminhos Azure e O WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267759"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Seleção de caminhos azure em vários links ISP

O Azure Virtual WAN fornece a um utilizador a capacidade de incluir informações de ligação num Site VPN, permitindo cenários em que a solução de dispositivo VPN/SD-WAN pode programar políticas específicas de ramo para orientar o tráfego através de várias ligações para o Azure. Isto chama-se **seleção de caminhos Azure.**

## <a name="architecture"></a>Arquitetura

Para entender como funciona a seleção do caminho Azure, vamos usar o exemplo de um site virtual WAN VPN e uma ligação site-to-site.

Um site VPN representa o dispositivo SD-WAN/VPN no local com informações como IP público, modelo de dispositivo e nome, etc. O site VPN real no local pode ter vários links ISP que também podem ser incluídos nas informações do site DoRP Virtual WAN. Isto permite-lhe visualizar a informação de link em Azure.

Uma ligação iPsec site-to-site que entra numa VPN virtual da WAN termina nas instâncias de gateway VPN dentro de um hub virtual. Uma ligação site-to-site representa a conectividade entre o site VPN e o gateway Azure VPN. Consiste em uma ou mais ligações de ligação. Cada ligação de ligação consiste em dois túneis com cada túnel terminando num exemplo único do gateway Azure Virtual WAN VPN. Até quatro ligações de ligação podem ser configurada na ligação local-local, o que permite ter até oito túneis dentro de uma ligação local-local. O Azure suporta até 2000 túneis terminando dentro de uma única porta virtual WAN VPN.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagrama multi-ligação":::

Este número mostra multi-link num site que se liga ao Azure Virtual WAN. Neste diagrama:

* Existem duas ligações ISP no ramo no local (dispositivo VPN/SD-WAN). Cada ligação ISP corresponde a uma ligação de ligação.

* Assumiu que o dispositivo VPN/SD-WAN, gestor de clientes no local, suporta o IKEv1 ou o IKEv2 IPsec.

* Cada ligação Azure site-to-site Virtual WAN é composta por ligações de ligação dentro de si. Uma ligação suporta até quatro ligações de ligação. A Azure cobra uma taxa de unidade de ligação para a ligação VIRTUAL WAN. Não há qualquer custo para as ligações de ligação.

* Cada ligação de ligação, por sua vez, consiste em dois túneis IPsec que podem terminar em duas instâncias diferentes do gateway VPN Virtual WAN. Os gateways são criados como portais ativos para a resiliência. Cada ligação de ligação é necessária para ter um endereço IP único e IP de peering BGP. No diagrama, o túnel 0 pode terminar no caso 0, e o túnel 1 pode terminar no caso 1.

* Os dispositivos de ramificação que fornecem a seleção de caminhos podem permitir uma política adequada na solução de gestão da sucursal para orientar o tráfego através de múltiplas ligações ao Azure. Por exemplo, o link ISP 1 pode ser utilizado para tráfego prioritário mais elevado e a ligação ISP 2 pode ser usada como cópia de segurança.

* É importante notar que o Hub VPN Virtual utiliza ecMP (encaminhamento multi-percurso de custo igual) em todos os túneis de terminação.

## <a name="next-steps"></a>Passos seguintes

Ver [a Azure FAQ](virtual-wan-faq.md).