---
title: 'Modificar as definições do endereço IP gateway: PowerShell'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local usando o PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 41157f26e4f87e59d5a0276ff72012ae20b70fbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84986006"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as definições do gateway de rede local com o PowerShell

Por vezes, as definições para a sua rede local addressPrefix ou GatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Before you begin

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP gateway

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximos passos

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).