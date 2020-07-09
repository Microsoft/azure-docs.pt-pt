---
title: 'Gateway VPN: Modificar as definições do endereço IP do gateway: Portal Azure'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local utilizando o portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fa43df8c4f17bff4e97d999c6653bdcb045bfec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84985215"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as definições do gateway de rede local com o portal do Azure

Por vezes, as definições para a sua rede local addressPrefix ou GatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

Antes de eliminar a ligação, pode querer descarregar a configuração para os seus dispositivos de ligação para obter o PSK definido. Assim, não precisas de o redefinir do outro lado.

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

Quando modifica os prefixos do endereço IP, os passos que segue dependem de se o gateway de rede local tem uma ligação.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP gateway

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Quando altera o endereço IP público, os passos que segue dependem de se o gateway de rede local tem uma ligação.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Próximos passos

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).
