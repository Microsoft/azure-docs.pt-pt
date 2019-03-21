---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/20/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b1a9d93d9fccf02ba1517e429625150736e539e9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305377"
---
Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA. Para o gateway de rede virtual SKUs em zonas de disponibilidade do Azure, consulte [SKUs de Gateway de zonas de disponibilidade do Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKUs de gateway por conjunto de recursos

SKU de gateway VPN novo simplificam os conjuntos de funcionalidades oferecidos em gateways:

| **SKU**| **Funcionalidades**|
| ---    | ---         |
|**Básico** (*)   | **VPN baseada em rota**: 10 túneis de S2S/ligações; sem autenticação RADIUS para P2S; sem IKEv2 para P2S<br>**VPN baseada em política**: (IKEv1): 1 túnel S2S/ligação; nenhum P2S|
| **VpnGw1, VpnGw2, and VpnGw3** | **VPN baseada em rota**: até 30 túneis (*), P2S, BGP, ativo-ativo, IPsec/IKE política personalizada, coexistência ExpressRoute/VPN |
|        |             |

( * ) Pode configurar "PolicyBasedTrafficSelectors" para se ligar a um gateway de VPN baseada em rota (VpnGw1, VpnGw2, VpnGw3) para vários dispositivos com firewall baseada na política no local. Consulte [Gateways de ligação VPN para vários dispositivos VPN com base na política local com o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

(\*\*) O SKU básico é considerado um SKU de legado. O SKU básico tem algumas limitações de recursos. Não pode redimensionar um gateway que utiliza um SKU básico para um dos novos SKUs de gateway, em vez disso, tem de alterar para um novo SKU, que envolve a eliminar e recriar o gateway de VPN.

###  <a name="workloads"></a>SKUs de gateway - vs de produção. Cargas de trabalho Dev-Test

Devido a diferenças na SLAs e conjuntos de recursos, é recomendável seguir os SKU para produção vs. dev / test:

| **Carga de trabalho**                       | **SKU**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | VpnGw1, VpnGw2, VpnGw3 |
| **Dev-test ou prova de conceito**   | Basic (*)                 |
|                                    |                        |

(\*\*) O SKU básico é considerado um SKU de legado e tem limitações de recursos. Certifique-se de que o recurso que precisa é suportado antes de utilizar o SKU básico.

Se estiver a utilizar os SKU antigos (Legado), as recomendações de SKU de produção são padrão e HighPerformance. Para obter informações e instruções para a SKU antigos, consulte [SKUs de Gateway (Legado)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
