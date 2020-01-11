---
title: 'Como configurar o OpenVPN no gateway de VPN do Azure: PowerShell'
description: Etapas para configurar o OpenVPN para o gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 02514285570b764c6f7392b789f2ff7b427bb3a6
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863762"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurar o OpenVPN para gateway de VPN ponto a site do Azure

Este artigo ajuda você a configurar o **protocolo OpenVPN®** no gateway de VPN do Azure. O artigo pressupõe que você já tenha um ambiente de ponto a site de trabalho. Se você não fizer isso, use as instruções na etapa 1 para criar uma VPN ponto a site.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. criar uma VPN ponto a site

Se você ainda não tiver um ambiente de ponto a site em funcionamento, siga a instrução para criar um. Consulte [criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com autenticação de certificado nativa do Azure. 

> [!IMPORTANT]
> O SKU básico não tem suporte para OpenVPN.

## <a name="enable"></a>2. habilitar OpenVPN no gateway

Habilite OpenVPN em seu gateway. Verifique se o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os seguintes comandos:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passos seguintes

Para configurar clientes para o OpenVPN, consulte [configurar clientes OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
