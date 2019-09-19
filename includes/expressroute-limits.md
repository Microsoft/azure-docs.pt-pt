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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622117"
---
| Resource | Limite máximo/padrão |
| --- | --- |
| Circuitos de ExpressRoute por assinatura |10 |
| Circuitos de ExpressRoute por região por assinatura, com Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o padrão de ExpressRoute |4,000 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o complemento Premium do ExpressRoute |10,000 |
| Número máximo de rotas anunciadas do emparelhamento privado do Azure do espaço de endereço da VNet para uma conexão do ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o padrão de ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o complemento Premium do ExpressRoute |200 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual no mesmo local de emparelhamento |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em locais de emparelhamento diferentes |4 |
| Número de links de rede virtual permitidos por circuito de ExpressRoute |Consulte o [número de redes virtuais por tabela de circuito do ExpressRoute](#vnetpercircuit) .  |

#### <a name="vnetpercircuit"></a>Número de redes virtuais por circuito de ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtual para Standard** | **Número de links de rede virtual com complemento Premium** |
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
| 100 Gbps * |10 |100 |

**100 Gbps ExpressRoute somente direto*
