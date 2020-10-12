---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854058"
---
| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefixos de endereço VNet                   | 600 por gateway VPN          |
| Rotas BGP agregadas                    | 4.000 por porta de entrada VPN        |
| Prefixos de endereço de gateway de rede local  | 1000 por porta de entrada de rede local               |
| Ligações S2S                         | [Depende do gateway SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Ligações P2S                         | [Depende do gateway SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limite de rota P2S - IKEv2                 | 256 para não-Windows **/** 25 para Windows           |
| Limite de rota P2S - OpenVPN               | 1000                         |
| Um máximo de flows                              | 100K para VpnGw1/AZ  **/**  512K para VpnGw2-4/AZ|