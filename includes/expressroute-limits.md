---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147801"
---
| Recurso | Limite |
| --- | --- |
| Circuitos ExpressRoute por subscrição |10 |
| Circuitos ExpressRoute por região por subscrição, com Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para Azure private peering com ExpressRoute Standard |4000 |
| Número máximo de rotas anunciadas para a Azure private peering com expressRoute Premium add-on |10,000 |
| Número máximo de rotas anunciadas a partir do Azure private olhando do espaço de endereço VNet para uma ligação ExpressRoute |200 |
| Número máximo de rotas anunciadas para a Microsoft a espreitar com o ExpressRoute Standard |200 |
| Número máximo de rotas anunciadas para a Microsoft a espreitar com o add-on ExpressRoute Premium |200 |
| Número máximo de circuitos ExpressRoute ligados à mesma rede virtual no mesmo local de observação |4 |
| Número máximo de circuitos ExpressRoute ligados à mesma rede virtual em diferentes locais de observação |4 |
| Número de ligações de rede virtuais permitidas por circuito ExpressRoute |Consulte o número de redes virtuais por tabela [de circuito ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Número de redes virtuais por circuito ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtuais para Standard** | **Número de ligações de rede virtuais com add-on Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct Only*

> [!NOTE]
> As ligações Global Reach contam contra o limite das ligações de rede virtuais por Circuito ExpressRoute. Por exemplo, um Circuito Premium de 10 Gbps permitiria 5 ligações Global Reach e 95 ligações aos Gateways ExpressRoute ou 95 ligações Global Reach e 5 ligações aos Gateways ExpressRoute ou qualquer outra combinação até ao limite de 100 ligações para o circuito.
