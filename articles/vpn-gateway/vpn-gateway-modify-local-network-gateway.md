---
title: Modificar os prefixos de endereço IP de gateway de rede local e o endereço IP do Gateway de VPN | Azure | PowerShell | Documentos da Microsoft
description: Este artigo explica-lhe alterar os prefixos de endereços IP para o seu gateway de rede local com o PowerShell
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
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 923c478909e0393f36da06802023b3f40ee63a3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723892"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as definições do gateway de rede local com o PowerShell

Por vezes, alterar as definições para o seu gateway de rede local AddressPrefix ou GatewayIPAddress. Este artigo mostra como modificar as definições do gateway de rede local. Também pode modificar essas configurações usando um método diferente ao selecionar uma opção diferente na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="ipaddprefix"></a>Modificar prefixos de endereços IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Pode verificar a ligação de gateway. Ver [Certifique-se de uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).