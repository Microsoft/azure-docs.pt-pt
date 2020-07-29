---
title: Como configurar clientes OpenVPN para a Azure VPN Gateway Microsoft Docs
description: Passos para configurar clientes OpenVPN para Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984084"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para Azure VPN Gateway

Este artigo ajuda-o a configurar clientes ** &reg; do Protocolo OpenVPN.**

## <a name="before-you-begin"></a>Before you begin

Verifique se preencheu os passos para configurar o OpenVPN para o seu gateway VPN. Para mais detalhes, consulte [o Configure OpenVPN para O Gateway Azure VPN](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximos passos

Se quiser que os clientes VPN possam aceder aos recursos noutro VNet, siga as instruções do artigo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma ligação vnet-to-vnet. Certifique-se de que ativa o BGP nos gateways e nas ligações, caso contrário o tráfego não fluirá.

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
