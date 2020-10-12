---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9734859c0bf22201c146e5d8a220f3146f6051c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184149"
---
A tabela seguinte mostra os tipos de gateways e o débito agregado estimado pelo SKU do gateway. Esta tabela aplica-se ao Gestor de Recursos e aos modelos clássicos de implantação. 

Há diferença de preços entre os SKUs de gateway. Para obter mais informações, veja [Preços do Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).

Tenha em atenção que o SKU do gateway UltraPerformance não está representado nesta tabela. Para obter informações sobre o SKU de UltraPerformance, consulte a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

|  | **Débito do VPN Gateway (1)** | **Máximo de túneis IPsec do VPN Gateway (2)** | **Débito do Gateway do ExpressRoute** | **Coexistência do ExpressRoute e do VPN Gateway** |
| --- | --- | --- | --- | --- |
| **SKU Básico (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |Não |
| **SKU Padrão (4)(5)** |100 Mbps |10 |1000 Mbps |Sim |
| **SKU de Elevado Desempenho (4)** |200 Mbps |30 |2000 Mbps |Sim |


(1) O débito da VPN é uma estimativa aproximada baseada em medidas entre VNets na mesma região do Azure. Não é um débito garantido para ligações entre locais na Internet. É a medida de débito mais alta possível.

(2) O número de túneis refere-se às VPNs RouteBased. Uma VPN PolicyBased só pode suportar um túnel VPN de Rede de VPNs.

(3) O BGP não é suportado para o SKU Básico.

(4) As VPNs PolicyBased não são suportadas para este SKU. Só são suportadas para o SKU Básico.

(5) As ligações do Gateway de VPN S2S no modo ativo/ativo não são suportadas por este SKU. O modo ativo/ativo é suportado apenas no SKU HighPerformance.

(6) O SKU básico é precotado para utilização com o ExpressRoute.
