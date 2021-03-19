---
title: Como configurar o OpenVPN para o Azure VPN Gateway
description: Saiba como ativar o Protocolo OpenVPN no Gateway Azure VPN para um ambiente ponto-a-local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584045"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Configure OpenVPN para gateways VPN ponto-a-local

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

## <a name="powershell"></a>PowerShell

1. Ativar o OpenVPN no seu gateway utilizando o seguinte exemplo:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continue com **os próximos passos**.

## <a name="next-steps"></a>Passos seguintes

Para configurar clientes para a OpenVPN, consulte os [clientes Configure OpenVPN.](vpn-gateway-howto-openvpn-clients.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
