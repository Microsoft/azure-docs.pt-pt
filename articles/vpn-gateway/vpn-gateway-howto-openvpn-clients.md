---
title: Como configurar clientes OpenVPN para o Azure VPN Gateway. Microsoft Docs
description: Passos para configurar clientes OpenVPN para O Gateway VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066144"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configure clientes OpenVPN para Azure VPN Gateway

Este artigo ajuda-o a configurar clientes do **OpenVPN &reg; Protocol.**

## <a name="before-you-begin"></a>Antes de começar

Verifique se completou os passos para configurar o OpenVPN para o seu gateway VPN. Para mais detalhes, consulte [Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passos seguintes

Se quiser que os clientes VPN possam aceder a recursos noutro VNet, siga as instruções do artigo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma ligação vnet-to-vnet. Certifique-se de que ativa o BGP nos portões e nas ligações, caso contrário o tráfego não fluirá.

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
