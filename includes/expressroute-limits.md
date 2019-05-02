---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733754"
---
| Recurso | Limite de máximo/predefinido |
| --- | --- |
| Circuitos do ExpressRoute por subscrição |10 |
| Circuitos do ExpressRoute por região por subscrição, com o Azure Resource Manager |10 |
| Número máximo de rotas para peering privado do Azure com o ExpressRoute Standard |4,000 |
| Número máximo de rotas para peering privado do Azure com o suplemento ExpressRoute Premium |10,000 |
| Número máximo de rotas para peering privado do Azure do espaço de endereço VNet para uma ligação do ExpressRoute |200 | 
| Número máximo de rotas para peering da Microsoft Azure com o ExpressRoute Standard |200 |
| Número máximo de rotas para peering da Microsoft Azure com o suplemento ExpressRoute Premium |200 |
| Número máximo de circuitos do ExpressRoute ligado à mesma rede virtual na mesma localização de peering |4 |
| Número máximo de circuitos do ExpressRoute ligado à mesma rede virtual em diferentes localizações de peering |> 4, depende do tamanho da GatewaySubnet|
| Número de ligações de rede virtual permitidos por circuito do ExpressRoute |Consulte a tabela seguinte. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Número de redes virtuais por circuito do ExpressRoute
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

