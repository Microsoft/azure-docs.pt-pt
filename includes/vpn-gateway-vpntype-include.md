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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010881"
---
* **Política Integrada:** As VPNs de Política foram anteriormente chamadas de gateways de encaminhamento estático no modelo clássico de implantação. As VPNs baseadas em políticas encriptam e directizam pacotes através de túneis IPsec com base nas políticas IPsec configuradas com as combinações de prefixos de endereço entre a sua rede no local e o VNet Azure. Normalmente, a política (ou o seletor de tráfego), é definido como uma lista de acesso na configuração do dispositivo VPN. O valor para um tipo VPN baseado em política é *o PolicyBased*. Ao utilizar uma VPN VPN de 400% de política, tenha em mente as seguintes limitações:
  
  * As VPNs baseadas em políticas **só** podem ser utilizadas no Portal Básico SKU. Este tipo VPN não é compatível com outros SKUs de gateway.
  * Só pode ter um túnel quando utilizar uma VPN de VPN de sVPN.
  * Só pode utilizar VPNs baseados em Políticas para ligações S2S e apenas para determinadas configurações. A maioria das configurações do Gateway VPN requerem uma VPN RouteBased.
* **RouteBased**: RouteBased VPNs foram anteriormente chamados gateways dinâmicos de encaminhamento no modelo de implementação clássico. As VPNs routebas utilizam "rotas" na tabela de encaminhamento ou encaminhamento IP para direcionar os pacotes para as respetivas interfaces de túneis. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política (ou seletor de tráfego) para VPNs routebased são configuradas como qualquer qualquer -para-qualquer (ou cartões selvagens). O valor para um tipo VPN RouteBased é *RouteBased*.
