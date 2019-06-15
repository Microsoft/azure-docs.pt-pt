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
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077533"
---
Para criar rapidamente uma VNet, pode clicar em "Tentá-lo" neste artigo para abrir a consola do PowerShell no Azure Cloud Shell. Ajuste os valores e copie e cole os comandos na janela da consola. 

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
