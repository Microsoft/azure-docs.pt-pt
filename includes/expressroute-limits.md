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
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335106"
---
| Recurso | Limite |
| --- | --- |
| Circuitos ExpressRoute por subscrição |10 |
| Circuitos ExpressRoute por região por subscrição, com Gestor de Recursos Azure |10 |
| Número máximo de rotas anunciadas para o azure private peering com ExpressRoute Standard |4000 |
| Número máximo de rotas anunciadas para o azure private peering com add-on ExpressRoute Premium |10,000 |
| Número máximo de rotas anunciadas a partir do espaço de endereçoS VNet para uma ligação ExpressRoute |200 |
| Número máximo de rotas anunciadas para a Microsoft a espreitar com o ExpressRoute Standard |200 |
| Número máximo de rotas anunciadas para a Microsoft peering com add-on ExpressRoute Premium |200 |
| Número máximo de circuitos ExpressRoute ligados à mesma rede virtual no mesmo local de observação |4 |
| Número máximo de circuitos ExpressRoute ligados à mesma rede virtual em diferentes locais de observação |4 |
| Número de ligações de rede virtual permitidas por circuito ExpressRoute |Consulte o Número de redes virtuais por tabela de [circuitos ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Número de redes virtuais por circuito ExpressRoute
| **Tamanho do circuito** | **Número de ligações de rede virtual para Standard** | **Número de ligações de rede virtual com add-on Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct Apenas*

> [!NOTE]
> As ligações Global Reach contam contra o limite de ligações de rede virtual por Circuito ExpressRoute. Por exemplo, um Circuito Premium de 10 Gbps permitiria 5 ligações Global Reach e 95 ligações às Ligações ExpressRoute Gateways ou 95 ligações Global Reach e 5 ligações aos Gateways ExpressRoute ou qualquer outra combinação até ao limite de 100 ligações para o circuito.
