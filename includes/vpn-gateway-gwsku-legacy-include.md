---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77211379"
---
Os SKUs do gateway VPN legados (antigos) são:

* Predefinição (Básico)
* Standard
* HighPerformance

O Gateway de VPN não utiliza o SKU do gateway de UltraPerformance. Para obter informações sobre o SKU de UltraPerformance, consulte a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Ao trabalhar com os SKUs legados, considere o seguinte:

* Se pretender utilizar um tipo de VPN PolicyBased, tem de utilizar o SKU Básico. As VPNs PolicyBased (anteriormente designadas Encaminhamento Estático) não são suportadas em mais nenhum SKU.
* O BGP não é suportado no SKU Básico.
* As configurações coexistentes do Gateway de VPN do ExpressRoute não são suportadas no SKU Básico.
* As ligações do Gateway de VPN S2S do modo ativo/ativo podem ser configuradas apenas no SKU HighPerformance.