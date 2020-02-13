---
title: 'Como configurar o OpenVPN no Azure VPN Gateway: PowerShell'
description: Passos para configurar OpenVPN para Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162412"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configure OpenVPN para Azure ponto-a-site VPN Gateway

Este artigo ajuda-o a configurar o **OpenVPN® Protocolo** sobre o Gateway Azure VPN. O artigo assume que já tem um ambiente de trabalho ponto-a-local. Se não o fizer, utilize as instruções no passo 1 para criar uma VPN ponto-a-local.



## <a name="vnet"></a>1. Criar uma VPN ponto-a-local

Se ainda não tiver um ambiente de ponto a local funcional, siga as instruções para criar um. Consulte [Criar uma VPN ponto-a-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway VPN ponto-a-site com autenticação de certificado azure nativo. 

> [!IMPORTANT]
> O SKU Básico não é suportado para o OpenVPN.

## <a name="enable"></a>2. Ativar o OpenVPN na porta de entrada

Ative o OpenVPN na sua porta de entrada. Certifique-se de que o portal já está configurado para o ponto-a-local (IKEv2 ou SSTP) antes de executar os seguintes comandos:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passos seguintes

Para configurar clientes para OpenVPN, consulte [os clientes Configure OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
