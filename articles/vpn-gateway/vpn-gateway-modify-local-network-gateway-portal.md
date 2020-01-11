---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: portal do Azure'
description: Este artigo orienta você pela alteração de prefixos de endereço IP para seu gateway de rede local usando o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864031"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as configurações do gateway de rede local usando o portal do Azure

Às vezes, as configurações para o gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações do gateway de rede local. Você também pode modificar essas configurações usando um método diferente selecionando uma opção diferente na lista a seguir:

Antes de excluir a conexão, convém baixar a configuração para seus dispositivos de conexão para obter a PSK definida. Dessa forma, você não precisa redefini-lo no outro lado.

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Modificar prefixos de endereço IP

Quando você modifica prefixos de endereço IP, as etapas a serem seguidas dependem se o gateway de rede local tem uma conexão.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Quando você altera o endereço IP público, as etapas a serem seguidas dependem se o gateway de rede local tem uma conexão.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Passos seguintes

Você pode verificar sua conexão de gateway. Consulte [verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).
