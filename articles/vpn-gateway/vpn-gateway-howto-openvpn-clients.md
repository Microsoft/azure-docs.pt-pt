---
title: Como configurar clientes OpenVPN para a Azure VPN Gateway Microsoft Docs
description: Saiba como configurar clientes do Protocolo OpenVPN para os clientes Azure VPN Gateway, incluindo clientes Windows, Mac e Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926217"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para Azure VPN Gateway

Este artigo ajuda-o a configurar clientes ** &reg; do Protocolo OpenVPN.**

## <a name="before-you-begin"></a>Before you begin

Verifique se preencheu os passos para configurar o OpenVPN para o seu gateway VPN. Para mais detalhes, consulte [o Configure OpenVPN para O Gateway Azure VPN](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passos seguintes

Se quiser que os clientes VPN possam aceder aos recursos noutro VNet, siga as instruções do artigo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma ligação vnet-to-vnet. Certifique-se de que ativa o BGP nos gateways e nas ligações, caso contrário o tráfego não fluirá.

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
