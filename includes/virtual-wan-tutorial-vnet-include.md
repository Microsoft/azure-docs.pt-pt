---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552992"
---
Se ainda não tiver uma VNet, pode criar uma rapidamente com o PowerShell. Também pode criar uma rede virtual com o portal do Azure.

* Verifique que o espaço de endereços da VNet que criar não se sobrepõe a nenhum dos intervalos de endereços de outras VNets às quais pretenda ligar nem a nenhum dos espaços de endereços da sua rede no local. 
* Se já tiver uma VNet, confirme que a mesma cumpre os critérios obrigatórios e que não tem um gateway de rede virtual.

Pode criar facilmente a VNet ao clicar em “Experimentar”, no artigo, para abrir uma consola do PowerShell. Ajuste os valores e copie e cole os comandos na janela da consola.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Ajuste os comandos do PowerShell e crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Criar uma VNet

Ajuste os comandos do PowerShell para criar a VNet que é compatível com o seu ambiente.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```