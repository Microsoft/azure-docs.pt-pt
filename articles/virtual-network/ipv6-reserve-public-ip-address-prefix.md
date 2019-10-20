---
title: Reservar endereços IPv6 públicos e intervalos de endereços em uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como reservar endereços IPv6 públicos e intervalos de endereços em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595072"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reservar prefixo de endereço IPv6 público
O IPv6 para VNet (rede virtual) do Azure permite hospedar aplicativos no Azure com conectividade IPv6 e IPv4 tanto dentro de uma rede virtual quanto de e para a Internet. Além de reservar endereços IPv6 individuais, Reserve intervalos contíguos de endereços IPv6 do Azure (conhecido como prefixo IP) para seu uso. Este artigo descreve como criar endereços IP públicos IPv6 e intervalos de endereços usando o Azure PowerShell e a CLI.

> [!Important]
> O IPv6 para a rede virtual do Azure está atualmente em visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Criar um único IP público IPv6 reservado

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Você pode criar um único endereço IP público de IPv6 reservado (estático) usando Azure PowerShell com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) da seguinte maneira:

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

 Você pode criar um único endereço IP público de IPv6 reservado (estático) CLI do Azure com [AZ Network Public-IP Create](/cli/azure/network/public-ip) da seguinte maneira:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Criar um prefixo IPv6 reservado (intervalo)

Para reservar um prefixo IPv6, adicione a família de endereços IP do IPv6 ao mesmo comando usado para criar prefixos IPv4. Os comandos a seguir criam um prefixo de tamanho/125 (8 endereços IPv6).  

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Você pode criar um endereço IPv6 público usando CLI do Azure com [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) da seguinte maneira:
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

Você pode criar um endereço IPv6 público usando CLI do Azure da seguinte maneira:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Alocar um endereço IP público de um prefixo IPv6 reservado

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

 Você cria um IP público IPv6 estático a partir de um prefixo reservado adicionando o argumento `-PublicIpPrefix` ao criar o IP público usando Azure PowerShell. O exemplo a seguir pressupõe que um prefixo foi criado e armazenado em uma variável do PowerShell chamada: *$MyOwnIPv 6prefix*.

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
 
O exemplo a seguir pressupõe que um prefixo foi criado e armazenado em uma variável CLI chamada: *IPv6PrefixWestUS*.

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
- Saiba mais sobre o [prefixo de endereço IPv6](ipv6-public-ip-address-prefix.md).
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
