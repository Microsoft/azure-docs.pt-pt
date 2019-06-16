---
title: 'Como configurar OpenVPN no Gateway de VPN do Azure: PowerShell| Microsoft Docs'
description: Passos para configurar OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002950"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurar OpenVPN para o Gateway de VPN de ponto a site do Azure

Este artigo ajuda-o a configurar **OpenVPN® protocolo** no Gateway de VPN do Azure. O artigo pressupõe que já tem um ambiente de ponto a site de trabalho. Se não o fizer, utilize as instruções no passo 1 para criar uma VPN ponto a site.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Criar uma VPN ponto a site

Se ainda não tiver um ambiente de ponto a site está a funcionar, siga as instruções para criar um. Ver [criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificados nativa do Azure. 

> [!IMPORTANT]
> O SKU básico não é suportado para OpenVPN.

## <a name="enable"></a>2. Ativar OpenVPN no gateway

Ative OpenVPN no seu gateway. Certifique-se de que o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os seguintes comandos:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passos Seguintes

Para configurar clientes para OpenVPN, consulte [OpenVPN configurar clientes](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
