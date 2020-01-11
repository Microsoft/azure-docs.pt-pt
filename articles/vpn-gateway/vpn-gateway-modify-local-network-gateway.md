---
title: 'Modificar configurações de endereço IP do gateway: PowerShell'
description: Este artigo orienta você pela alteração de prefixos de endereço IP para seu gateway de rede local usando o PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864000"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as definições do gateway de rede local com o PowerShell

Às vezes, as configurações para o gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações do gateway de rede local. Você também pode modificar essas configurações usando um método diferente selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="ipaddprefix"></a>Modificar prefixos de endereço IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Você pode verificar sua conexão de gateway. Consulte [verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).