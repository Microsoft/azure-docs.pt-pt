---
title: Como configurar o OpenVPN no Gateway Azure VPN
description: Saiba como utilizar o PowerShell para ativar o Protocolo OpenVPN no Gateway Azure VPN para um ambiente ponto-a-local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393280"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configure OpenVPN para Azure ponto-a-local VPN Gateway

Este artigo ajuda-o a configurar **o Protocolo ® OpenVPN** no Gateway Azure VPN. Pode utilizar o portal ou as instruções PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

* O artigo assume que já tem um ambiente local de trabalho. Se não o fizer, crie um utilizando um dos seguintes métodos.

  * [Portal - Criar ponto a local](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell - Criar ponto a local](vpn-gateway-howto-point-to-site-rm-ps.md)

* Verifique se o seu gateway VPN não utiliza o SKU Básico. O SKU Básico não é suportado para a OpenVPN.

## <a name="portal"></a>Portal

1. No portal, navegue para a sua **configuração virtual de gateway de rede -> ponto a local**.
1. Para **o tipo de túnel,** selecione **OpenVPN (SSL)** a partir do dropdown.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Selecione OpenVPN SSL a partir do dropdown":::
1. Guarde as suas alterações e continue com **os próximos passos**.

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>Ativar o OpenVPN no seu gateway utilizando o PowerShell.

1. Ativar o OpenVPN no seu gateway utilizando o seguinte exemplo:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continue com **os próximos passos**.

## <a name="next-steps"></a>Passos seguintes

Para configurar clientes para a OpenVPN, consulte os [clientes Configure OpenVPN.](vpn-gateway-howto-openvpn-clients.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
