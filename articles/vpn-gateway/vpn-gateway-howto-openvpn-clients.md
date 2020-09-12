---
title: Como configurar clientes OpenVPN para a Azure VPN Gateway Microsoft Docs
description: Saiba como configurar o OpenVPN para os clientes do sistema operativo Azure VPN Gateway para clientes do sistema operativo Windows, Linux e Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435784"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurar clientes OpenVPN para Azure VPN Gateway

Este artigo ajuda-o a configurar clientes ** &reg; do Protocolo OpenVPN.**

## <a name="before-you-begin"></a>Before you begin

Verifique se preencheu os passos para configurar o OpenVPN para o seu gateway VPN. Para mais detalhes, consulte [o Configure OpenVPN para O Gateway Azure VPN](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximos passos

Se quiser que os clientes VPN possam aceder aos recursos noutro VNet, siga as instruções do artigo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma ligação vnet-to-vnet. Certifique-se de que ativa o BGP nos gateways e nas ligações, caso contrário o tráfego não fluirá.

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
