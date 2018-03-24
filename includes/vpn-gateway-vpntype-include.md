---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5de227e5de5ef9b41f6e0f64db86b7195259f7d6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
* **PolicyBased:** PolicyBased VPNs eram anteriormente denominadas gateways de encaminhamento estático no modelo de implementação clássica. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec baseados nas políticas IPsec configuradas com as combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o seletor de tráfego), é definido como uma lista de acesso na configuração do dispositivo VPN. O valor para um tipo de PolicyBased VPN é *PolicyBased*. Quando utilizar uma VPN PolicyBased, tenha em atenção as seguintes limitações:
  
  * Pode PolicyBased VPNs **apenas** ser utilizado no SKU de gateway básico. Este tipo VPN não é compatível com outros SKUs de gateway.
  * Pode ter apenas 1 túnel quando utilizar uma VPN PolicyBased.
  * Só pode utilizar PolicyBased VPNs para ligações S2S e apenas para determinados configurações. A maioria das configurações de Gateway de VPN requerem uma VPN RouteBased.
* **RouteBased**: RouteBased VPNs eram anteriormente denominadas gateways de encaminhamento dinâmico no modelo de implementação clássica. RouteBased VPNs utilizam "rotas" no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política (ou o Seletor de tráfego) para as VPNs de RouteBased estão configuradas como qualquer a qualquer (ou carateres universais). O valor para um tipo de RouteBased VPN é *RouteBased*.
