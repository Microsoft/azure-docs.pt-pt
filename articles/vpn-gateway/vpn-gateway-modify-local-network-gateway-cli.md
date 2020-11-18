---
title: 'Gateway VPN: Modificar as definições do endereço IP do gateway: Azure CLI'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local utilizando o Azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: aadb2107cd009ac58cf8edb12853b10a5a0e3677
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660003"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as definições de gateway de rede local utilizando o Azure CLI

Por vezes, as definições para a alteração do Prefixo do Endereço de Endereço de Gateway local ou do endereço IP gateway. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Before you begin

Instale a versão mais recente dos comandos CLI (2.0 ou mais tarde). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Próximos passos

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).