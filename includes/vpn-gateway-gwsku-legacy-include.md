---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159243"
---
Os SKUs do gateway VPN legados (antigos) são:

* Básica
* Standard
* HighPerformance

O Gateway de VPN não utiliza o SKU do gateway de UltraPerformance. Para obter informações sobre o SKU de UltraPerformance, consulte a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Ao trabalhar com os SKUs legados, considere o seguinte:

* Se pretender utilizar um tipo de VPN PolicyBased, tem de utilizar o SKU Básico. As VPNs PolicyBased (anteriormente designadas Encaminhamento Estático) não são suportadas em mais nenhum SKU.
* O BGP não é suportado no SKU Básico.
* As configurações coexistentes do Gateway de VPN do ExpressRoute não são suportadas no SKU Básico.
* As ligações do Gateway de VPN S2S do modo ativo/ativo podem ser configuradas apenas no SKU HighPerformance.