---
title: Como configurar clientes OpenVPN para a Azure VPN Gateway Microsoft Docs
description: Saiba como configurar o OpenVPN para os clientes do sistema operativo Azure VPN Gateway para clientes do sistema operativo Windows, Linux e Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036866"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para Azure VPN Gateway

Este artigo ajuda-o a configurar clientes ** &reg; do Protocolo OpenVPN.**

## <a name="before-you-begin"></a>Before you begin

Verifique se preencheu os passos para configurar o OpenVPN para o seu gateway VPN. Para mais detalhes, consulte [o Configure OpenVPN para O Gateway Azure VPN](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passos seguintes

Se quiser que os clientes VPN possam aceder aos recursos noutro VNet, siga as instruções do artigo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma ligação vnet-to-vnet. Certifique-se de que ativa o BGP nos gateways e nas ligações, caso contrário o tráfego não fluirá.

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
