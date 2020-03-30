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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184141"
---
* **Baseado em Políticas:** As VPNs baseadas em políticas foram anteriormente chamadas de gateways de encaminhamento estático no modelo clássico de implantação. As VPNs baseadas em políticas encriptam e pacotes diretos através de túneis IPsec com base nas políticas do IPsec configuradas com as combinações de prefixos de endereço entre a sua rede no local e o Azure VNet. Normalmente, a política (ou o seletor de tráfego), é definido como uma lista de acesso na configuração do dispositivo VPN. O valor para um tipo VPN baseado em políticas é *PolicyBased*. Ao utilizar uma VPN baseada em políticas, tenha em mente as seguintes limitações:
  
  * As VPNs baseadas em políticas **só** podem ser utilizadas no Gateway Basic SKU. Este tipo VPN não é compatível com outras SKUs de gateway.
  * Só pode ter um túnel de 1 quando utilizar uma VPN baseada em políticas.
  * Só é possível utilizar VPNs baseados em políticas para ligações S2S e apenas para determinadas configurações. A maioria das configurações de Gateway VPN requerem uma VPN baseada em rota.
* **RouteBased**: As VPNs baseadas em rotas foram anteriormente chamadas de gateways de encaminhamento dinâmico no modelo clássico de implantação. As VPNs baseadas em rotas utilizam "rotas" na tabela de encaminhamento ou encaminhamento ip para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política (ou seletor de tráfego) para VPNs baseados em rotas é configurada como qualquer um para qualquer (ou wild cards). O valor para um tipo VPN baseado em rota é *RouteBased*.
