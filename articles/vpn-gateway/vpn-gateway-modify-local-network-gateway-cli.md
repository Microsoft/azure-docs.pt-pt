---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: CLI do Azure'
description: Este artigo orienta você pela alteração de prefixos de endereço IP para seu gateway de rede local usando o CLI do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864048"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as configurações do gateway de rede local usando o CLI do Azure

Às vezes, as configurações para o prefixo de endereço do gateway de rede local ou o endereço IP do gateway são alteradas. Este artigo mostra como modificar as configurações do gateway de rede local. Você também pode modificar essas configurações usando um método diferente selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2,0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modificar prefixos de endereço IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Passos seguintes

Você pode verificar sua conexão de gateway. Consulte [verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).

