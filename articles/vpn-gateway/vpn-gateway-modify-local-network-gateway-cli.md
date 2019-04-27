---
title: Modificar os prefixos de endereço IP de gateway de rede local e o endereço IP do Gateway de VPN | Azure | CLI | Documentos da Microsoft
description: Este artigo orienta-o por meio de alterar os prefixos de endereços IP para o gateway de rede local com a CLI do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 20e8269f0ee7463dcff9a5f66e46ebd81cb3df84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652859"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as definições de gateway de rede local com a CLI do Azure

Por vezes, alterar as definições para o seu prefixo de endereço de gateway de rede local ou o endereço IP do Gateway. Este artigo mostra como modificar as definições do gateway de rede local. Também pode modificar essas configurações usando um método diferente ao selecionar uma opção diferente na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posteriores). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modificar prefixos de endereços IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Pode verificar a ligação de gateway. Ver [Certifique-se de uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).

