---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75152fabfc33dda0494d871fbdf9a388f4260c0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495715"
---
Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA. Para SKUs de gateway de rede virtual no Zonas de Disponibilidade do Azure, consulte [SKUs de gateway de zonas de disponibilidade do Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKUs de gateway por conjunto de recursos

As novas SKUs de gateway de VPN simplificam os conjuntos de recursos oferecidos nos gateways:

| **SKU**| **Funcionalidades**|
| ---    | ---         |
|**Básico** (* *)   | **VPN baseada em rota**: 10 túneis para S2S/conexões; nenhuma autenticação RADIUS para P2S; Não IKEv2 para P2S<br>**VPN baseada em políticas**: (IKEv1): 1 túnel S2S/conexão; sem P2S|
| **Todas as SKUs Generation1 e Generation2, exceto as básicas** | **VPN baseada em rota**: até 30 túneis (*), P2S, BGP, ativo-ativo, política de IPSec/IKE personalizada, coexistência de EXPRESSROUTE/VPN |
|        |             |

(*) Você pode configurar "PolicyBasedTrafficSelectors" para conectar um gateway de VPN baseado em rota a vários dispositivos de firewall baseados em políticas locais. Consulte [Gateways de ligação VPN para vários dispositivos VPN com base na política local com o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

(\*\*) O SKU básico é considerado um SKU herdado. A SKU básica tem certas limitações de recursos. Você não pode redimensionar um gateway que usa uma SKU básica para uma das novas SKUs de gateway. em vez disso, você deve alterar para uma nova SKU, o que envolve excluir e recriar o gateway de VPN.

###  <a name="workloads"></a>SKUs de gateway-produção versus desenvolvimento-cargas de trabalho de teste

Devido às diferenças em SLAs e conjuntos de recursos, recomendamos as seguintes SKUs para produção versus desenvolvimento/teste:

| **Carga de trabalho**                       | **SKU**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | Todas as SKUs Generation1 e Generation2, exceto as básicas |
| **Dev-test ou prova de conceito**   | Básico (* *)                 |
|                                    |                        |

(\*\*) O SKU básico é considerado um SKU herdado e tem limitações de recursos. Verifique se o recurso de que você precisa tem suporte antes de usar o SKU básico.

Se você estiver usando as SKUs antigas (herdadas), as recomendações de SKU de produção serão Standard e HighPerformance. Para obter informações e instruções para SKUs antigas, consulte [SKUs de gateway (Herdado)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
