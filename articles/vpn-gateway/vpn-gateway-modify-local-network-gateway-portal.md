---
title: 'Gateway VPN: Modificar as definições de endereço IP do gateway: Portal Azure'
description: Este artigo acompanha-o através da alteração dos prefixos de endereço IP para o seu portal de rede local utilizando o portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864031"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as definições de gateway da rede local utilizando o portal Azure

Por vezes, as definições para o seu gateway de rede local AddressPrefix ou gatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway da rede local. Também pode modificar estas definições utilizando um método diferente, selecionando uma opção diferente da seguinte lista:

Antes de eliminar a ligação, poderá descarregá-lo para os seus dispositivos de ligação para obter o PSK definido. Assim, não precisas de o redefinir do outro lado.

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

Quando modifica os prefixos do endereço IP, os passos que segue dependem se o gateway da rede local tem uma ligação.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP do gateway

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Quando muda o endereço IP público, os passos que segue dependem se o gateway da rede local tem uma ligação.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Passos seguintes

Pode verificar a ligação do portal. Ver [Verificar uma ligação](vpn-gateway-verify-connection-resource-manager.md)de gateway .
