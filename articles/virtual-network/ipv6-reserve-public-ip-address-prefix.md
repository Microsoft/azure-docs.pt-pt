---
title: Reserve endereços e intervalos de endereços e endereços públicos em uma rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba como reservar endereços e intervalos de endereços e endereços públicos numa rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88509948"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reserva pré-fixo de endereço IPv6 público
O IPv6 para Azure Virtual Network (VNet) permite-lhe hospedar aplicações em Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual como de e para a Internet. Além de reservar endereços IPv6 individuais, pode reservar gamas contíguas de endereços Azure IPv6 (conhecidos como PREfixo IP) para a sua utilização. Estes artigos descrevem como criar endereços IP públicos IPv6 e intervalos de endereços usando Azure PowerShell e CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Criar um único IP público reservado IPv6

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Pode criar um único endereço IP público reservado (estático) utilizando a Azure PowerShell com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) da seguinte forma:

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

 Pode criar um único endereço IP público reservado (estático) IPv6 Azure CLI com [rede az public-ip criar](/cli/azure/network/public-ip) da seguinte forma:

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Criar um prefixo IPv6 reservado (alcance)

Para reservar um prefixo IPv6, adicione a família de endereços IP do IPv6 ao mesmo comando utilizado para a criação de prefixos IPv4. Os seguintes comandos criam um prefixo de tamanho /125 ( endereços 8 IPv6).

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Pode criar um endereço IPv6 público utilizando o Azure CLI com [a rede az public-ip criar](/powershell/module/az.network/new-azpublicipprefix) da seguinte forma:
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

 Cria um IP público estático IPv6 a partir de um prefixo reservado adicionando o `-PublicIpPrefix` argumento ao criar o IP público usando Azure PowerShell. O exemplo a seguir pressupõe que um prefixo foi criado e armazenado numa variável PowerShell denominada: *$myOwnIPv 6Prefixo*.

```azurepowershell
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

O exemplo a seguir pressupõe que um prefixo foi criado e armazenado numa variável CLI denominada: *IPv6PrefixWestUS*.

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
- Saiba mais sobre [o prefixo do endereço IPv6](ipv6-public-ip-address-prefix.md).
- Saiba mais sobre [os endereços IPv6](ipv6-overview.md).
