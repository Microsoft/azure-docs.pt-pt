---
title: 'Como configurar o OpenVPN no Gateway Azure VPN: PowerShell'
description: Saiba como utilizar o PowerShell para ativar o Protocolo OpenVPN no Gateway Azure VPN para um ambiente ponto-a-local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: c13d14ad2d06cbc43d80c05258bdbd3303da4838
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036832"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configure OpenVPN para Azure ponto-a-local VPN Gateway

Este artigo ajuda-o a configurar **o Protocolo ® OpenVPN** no Gateway Azure VPN. O artigo assume que já tem um ambiente local de trabalho. Caso contrário, utilize as instruções no passo 1 para criar uma VPN ponto a local.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Criar uma VPN ponto-a-local

Se ainda não tiver um ambiente local funcional, siga as instruções para criar um. Consulte [Criar uma VPN ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar uma porta VPN ponto a local com autenticação de certificado Azure nativo. 

> [!IMPORTANT]
> O SKU Básico não é suportado para a OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Ativar a OpenVPN no gateway

Ative o OpenVPN no seu gateway. Certifique-se de que o gateway já está configurado para ponto a local (IKEv2 ou SSTP) antes de executar os seguintes comandos:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passos seguintes

Para configurar clientes para a OpenVPN, consulte os [clientes Configure OpenVPN.](vpn-gateway-howto-openvpn-clients.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
