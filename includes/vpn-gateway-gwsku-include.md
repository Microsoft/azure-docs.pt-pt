---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085266"
---
Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA. Para gateway de rede virtual SKUs em Zonas de Disponibilidade Azure, consulte As Zonas de [Disponibilidade Azure Gateway SKUs](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKUs (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 e VpnGw5AZ) são suportados apenas para o modelo de implantação do Gestor de Recursos. As redes virtuais clássicas devem continuar a utilizar as antigas (legados) SKUs.
>  * Para obter informações sobre o trabalho com o legado gateway SKUs (Basic, Standard e HighPerformance), consulte [Trabalhar com vpn gateway SKUs (legacy SKUs)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Para o gateway ExpressRoute SKUs, consulte [Gateways de rede virtual para ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway SKUs por conjunto de funcionalidades

O novo gateway VPN SKUs agiliza os conjuntos de funcionalidades oferecidos nos gateways:

| **SKU**| **Funcionalidades**|
| ---    | ---         |
|**Básico** (**)   | **VPN baseada na rota:** 10 túneis para S2S/ligações; Sem autenticação RADIUS para P2S; sem IKEv2 para P2S<br>**VPN baseada na política**: (IKEv1): 1 S2S/túnel de ligação; sem P2S|
| **Todas as Gerações1 e Geração2 SKUs, exceto Básico** | **VPN baseada em rota**: até 30 túneis (*), P2S, BGP, política ativa, personalizada IPsec/IKE, Coexistência ExpressRoute/VPN |
|        |             |

(*) Pode configurar "PolicyBasedTrafficSelectors" para ligar uma porta VPN baseada em rotas a vários dispositivos de firewall baseados em políticas no local. Consulte [Gateways de ligação VPN para vários dispositivos VPN com base na política local com o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

()\*\*O SKU básico é considerado um legado SKU. O SKU Básico tem certas limitações de funcionalidades. Não é possível redimensionar um portal que utilize um SKU Básico para uma das novas SKUs de gateway, deve mudar para um novo SKU, que envolve apagar e recriar o seu gateway VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway SKUs - Produção vs. Dev-Test Workloads

Devido a diferenças nos SLA e nos conjuntos de funcionalidades, é recomendável seguir os SKU para produção versus dev-test:

| **Carga de trabalho**                       | **SKU**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | Todas as Gerações1 e Geração2 SKUs, exceto Básico |
| **Dev-test ou prova de conceito**   | Básico (**)                 |
|                                    |                        |

()\*\*O SKU básico é considerado um Legado SKU e tem limitações de características. Verifique se a funcionalidade de que necessita é suportada antes de utilizar o SKU Básico.

Se estiver a utilizar as antigas SKUs (legado), as recomendações de produção da SKU são Standard e HighPerformance. Para obter informações e instruções para as antigas [SKUs, consulte Gateway SKUs (legado)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
