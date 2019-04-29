---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 555a8e3e92dc1d12cb7c6d6e06d2511f15a2c862
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679393"
---
|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Ligações SSTP <br>P2S** | **Ligações IKEv2 <br>P2S** | **Referência de<br>Débito de Agregação** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Básica** | Um máximo de 10    | Um máximo de 128  | Não suportado  | 100 Mbps  | Não suportado|
|**VpnGw1**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportadas |
|**VpnGw2**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportadas |
|**VpnGw3**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportadas |


(*) Utilize a [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis S2S VPN.

* A Referência de Débito de Agregação baseia-se em medidas de vários túneis agregados através de um único gateway. A Referência de Débito Agregado para um Gateway de VPN é uma combinação de S2S + P2S. **Se tiver muitas ligações P2S, isso pode afetar negativamente uma ligação S2S devido às limitações de débito.** A referência de débito de agregação não é uma taxa de transferência garantida devido a condições de tráfego de Internet e comportamentos da sua aplicação.

* Esses limites de ligação são separados. Por exemplo, pode ter 128 ligações SSTP e também 250 ligações IKEv2 num SKU VpnGw1.

* As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Os VpnGw1, VpnGw2 e VpnGw3 são suportados para gateways de VPN com o modelo de implementação do gestor de recursos.

* O SKU básico é considerado um SKU de legado. O SKU básico tem algumas limitações de recursos. Não pode redimensionar um gateway que utiliza um SKU básico para um dos novos SKUs de gateway, em vez disso, tem de alterar para um novo SKU, que envolve a eliminar e recriar o gateway de VPN. Certifique-se de que o recurso que precisa é suportado antes de utilizar o SKU básico.
