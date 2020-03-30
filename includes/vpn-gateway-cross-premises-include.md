---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "66425729"
---
|  | **Ponto-a-Local** | **Site-a-site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Serviços Suportados pelo Azure** |Cloud Services e Virtual Machines |Cloud Services e Virtual Machines |[Lista de serviços](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Larguras de Banda Típicas** |Com base no SKU de gateway |Normalmente, < 1 Gbps agregado |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protocolos Suportados** |Protocolo de Túnel de Tomadas Seguras (SSTP), OpenVPN e IPsec |IPsec |Ligação direta por VLANs, tecnologias VPN do NSP (MPLS, VPLS,...) |
| **Encaminhamento** |RouteBased (dinâmico) |Suportamos o roteamento PolicyBased (encaminhamento estático) e RouteBased (VPN de encaminhamento dinâmico) |BGP |
| **Resiliência de ligação** |ativa-passiva |ativa-passiva ou ativa-ativa |ativa-ativa |
| **Caso de utilização típica** |Criar protótipos, cenários de desenvolvimento/teste/laboratório para serviços em nuvem e máquinas virtuais |Cenários de desenvolvimento/teste/laboratório e cargas de trabalho de produção de pequena escala para serviços em nuvem e máquinas virtuais |Acesso a todos os serviços do Azure (lista validada), Cargas de trabalho de classe empresarial fundamentais, Cópia de segurança, Macrodados, Azure como um site de DR |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Preços** |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentação Técnica** |[Documentação do VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **FAQ** |[FAQ do VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[FAQ do VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[FAQ do ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
