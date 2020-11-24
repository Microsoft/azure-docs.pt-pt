---
title: incluir ficheiro
description: incluir ficheiro
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554849"
---
| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefixos de endereço VNet                   | 600 por gateway VPN          |
| Rotas BGP agregadas                    | 4.000 por porta de entrada VPN        |
| Prefixos de endereço de gateway de rede local  | 1000 por porta de entrada de rede local               |
| Ligações S2S                         | [Depende do gateway SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Ligações P2S                         | [Depende do gateway SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limite de rota P2S - IKEv2                 | 256 para não-Windows **/** 25 para Windows           |
| Limite de rota P2S - OpenVPN               | 1000                         |
| Um máximo de flows                              | 100K para VpnGw1/AZ  **/**  512K para VpnGw2-4/AZ|