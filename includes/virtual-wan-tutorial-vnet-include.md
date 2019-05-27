---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150841"
---
Para criar rapidamente uma VNet, pode clicar em "Tentá-lo" neste artigo para abrir a consola do PowerShell. Ajuste os valores e copie e cole os comandos na janela da consola. Para saber mais sobre o novo módulo Az e AzureRM compatibilidade, veja [apresentando o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az). Para instruções de instalação do módulo de Az, consulte [instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Verifique que o espaço de endereços da VNet que criar não se sobrepõe a nenhum dos intervalos de endereços de outras VNets às quais pretenda ligar nem a nenhum dos espaços de endereços da sua rede no local.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se ainda não tiver um grupo de recursos que pretende utilizar, crie um novo. Ajustar os comandos do PowerShell para refletir o nome do grupo de recursos que pretende utilizar e, em seguida, execute o seguinte cmdlet:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Criar uma VNet

Ajuste os comandos do PowerShell para criar uma VNet que é compatível para o seu ambiente.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```