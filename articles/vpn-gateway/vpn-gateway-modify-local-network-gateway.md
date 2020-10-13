---
title: 'Modificar as definições do endereço IP gateway: PowerShell'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local usando o PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: b235d278cb3061f17068f4e5a3edf5e8f8899f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392464"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as definições do gateway de rede local com o PowerShell

Por vezes, as definições para a sua rede local addressPrefix ou GatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP gateway

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).