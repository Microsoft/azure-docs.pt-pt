---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622117"
---
| Resource | Limite de máximo/predefinido |
| --- | --- |
| Circuitos do ExpressRoute por subscrição |10 |
| Circuitos do ExpressRoute por região por subscrição, com o Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para peering privado do Azure com o ExpressRoute Standard |4,000 |
| Número máximo de rotas anunciadas para peering privado do Azure com o suplemento ExpressRoute Premium |10,000 |
| Número máximo de rotas anunciadas nos peering privado do Azure do espaço de endereço VNet para uma ligação do ExpressRoute |200 |
| Número máximo de rotas anunciadas para peering da Microsoft com o ExpressRoute Standard |200 |
| Número máximo de rotas anunciadas para peering da Microsoft com o suplemento ExpressRoute Premium |200 |
| Número máximo de circuitos do ExpressRoute ligado à mesma rede virtual na mesma localização de peering |4 |
| Número máximo de circuitos do ExpressRoute ligado à mesma rede virtual em diferentes localizações de peering |4 |
| Número de ligações de rede virtual permitidos por circuito do ExpressRoute |Consulte a [número de redes virtuais por circuito ExpressRoute](#vnetpercircuit) tabela.  |

#### <a name="vnetpercircuit"></a> Número de redes virtuais por circuito do ExpressRoute
| **Tamanho do circuito** | **Número de ligações de rede virtual para o Standard** | **Número de ligações de rede virtual com o suplemento Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| Gbps de 100 * |10 |100 |

**Apenas o Direct de ExpressRoute 100 Gbps*
