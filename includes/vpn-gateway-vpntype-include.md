---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859087"
---
* **PolicyBased:** As VPNs PolicyBased eram anteriormente denominadas gateways de encaminhamento estático no modelo de implementação clássica. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec com base em políticas IPsec configuradas com as combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o seletor de tráfego), é definido como uma lista de acesso na configuração do dispositivo VPN. É o valor para um tipo de PolicyBased VPN *PolicyBased*. Quando utilizar uma VPN PolicyBased, tenha em atenção as seguintes limitações:
  
  * As VPNs PolicyBased podem **apenas** ser utilizado no SKU de gateway básico. Este tipo VPN não é compatível com outros SKUs de gateway.
  * Pode ter apenas 1 túnel quando utilizar uma VPN PolicyBased.
  * Só pode utilizar as VPNs PolicyBased para ligações de S2S e apenas para determinadas configurações. A maioria das configurações de Gateway de VPN requerem uma VPN RouteBased.
* **RouteBased**: RouteBased VPNs eram anteriormente denominadas gateways de encaminhamento dinâmico no modelo de implementação clássica. RouteBased VPNs utilizam "rotas" no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes em suas interfaces de túnel correspondente. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política (ou o Seletor de tráfego) para as RouteBased VPNs estão configurados como qualquer a qualquer (ou carateres universais). É o valor para um tipo de RouteBased VPN *RouteBased*.