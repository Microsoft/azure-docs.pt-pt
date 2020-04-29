---
title: Reserve endereços públicos iPv6 e endereços numa rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba como reservar endereços públicos iPv6 e gamas de endereços numa rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420550"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reserve prefixo de endereço iPv6 público
O IPv6 para a Rede Virtual Azure (VNet) permite-lhe hospedar aplicações em Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual como de e para a Internet. Além de reservar endereços IPv6 individuais, pode reservar gamas contíguas de endereços Azure IPv6 (conhecido sip Prefix) para a sua utilização. Estes artigos descrevem como criar endereços IP públicos iPv6 e gamas de endereços usando Azure PowerShell e CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Crie um único IP público iPv6 reservado

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Pode criar um único endereço IP (estático) IPv6, utilizando o Azure PowerShell com [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) da seguinte forma:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

 Pode criar um único endereço IP público IPv6 (estático) IPv6 Azure CLI com [rede az public-ip criar](/cli/azure/network/public-ip) da seguinte forma:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Criar um prefixo IPv6 reservado (gama)

Para reservar um prefixo IPv6, adicione a família de endereçoip do IPv6 ao mesmo comando utilizado para criar prefixos IPv4. Os seguintes comandos criam um prefixo de tamanho /125 (8 endereços IPv6).  

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Pode criar um endereço IPv6 público utilizando o Azure CLI com a [rede az public-ip criar](/powershell/module/az.network/new-azpublicipprefix) da seguinte forma:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Pode criar um endereço IPv6 público utilizando o Azure CLI da seguinte forma:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Alocar um endereço IP público a partir de um Prefixo IPv6 reservado

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

 Cria um IP público iPv6 estático a `-PublicIpPrefix` partir de um prefixo reservado adicionando o argumento ao criar o IP público utilizando o Azure PowerShell. O exemplo seguinte pressupõe que um prefixo foi criado e armazenado numa variável PowerShell chamada: *$myOwnIPv6Prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
 
O exemplo seguinte pressupõe que um prefixo foi criado e armazenado numa variável CLI chamada: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o prefixo de [endereço IPv6](ipv6-public-ip-address-prefix.md).
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
