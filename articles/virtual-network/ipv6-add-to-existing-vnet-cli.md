---
title: Adicione iPv6 a uma aplicação IPv4 na rede virtual Azure - Azure CLI
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar endereços IPv6 para uma aplicação existente na rede virtual Azure usando o Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f3f9b32ea55f0ceebf08b22ccc7e2ceec0b6227e
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420792"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Adicione iPv6 a uma aplicação IPv4 na rede virtual Azure - Azure CLI

Este artigo mostra-lhe como adicionar endereços IPv6 a uma aplicação que está a usar o endereço IP público IPv4 numa rede virtual Azure para um Balancer de Carga Padrão utilizando o Azure CLI. A atualização em vigor inclui uma rede virtual e uma subnet, um Balancer de Carga Padrão com configurações frontais IPv4 + IPV6, VMs com NICs que têm configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 do Azure CLI ou posterior. Para encontrar a sua `az --version`versão instalada, corra. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que implementou um Balancer de Carga Padrão como descrito em [Quickstart: Create a Standard Load Balancer - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Criar endereços IPv6

Crie um endereço IPv6 público com a [rede Az public-ip criar](/cli/azure/network/public-ip) para o seu Standard Load Balancer. O exemplo seguinte cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configure IPv6 balancer frontal

COnfigure o equilibrador de carga com o novo endereço IP IPv6 utilizando [a rede az lb frontend-ip criar](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) da seguinte forma:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configure IPv6 piscina de backend de balanceador de carga

Crie o pool de backend para NICs com endereços IPv6 utilizando o [endereço-pool da rede az lb criar](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) o seguinte:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configure as regras do equilíbrio de carga IPv6

Crie regras de equilíbrio de carga IPv6 com [a regra lb da rede az criar](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Adicione gamas de endereços IPv6

Adicione as gamas de endereços IPv6 à rede virtual e subnet ahospedar o equilíbrio de carga da seguinte forma:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Adicione a configuração IPv6 aos NICs

Configure os NICs VM com um endereço IPv6 utilizando [a rede az nic ip-config criar](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) da seguinte forma:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de dupla pilha IPv6 no portal Azure
Pode ver a rede virtual iPv6 dual stack no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, entre no *myVnet.*
2. Quando o **myVnet** aparecer nos resultados da pesquisa, selecione-o. Isto lança a página **de visão geral** da rede virtual dual stack chamada *myVNet*. A rede virtual dual stack mostra os três NICs com configurações IPv4 e IPv6 localizadas na subnet dual stack chamada *mySubnet*.

  ![Rede virtual de dupla pilha IPv6 em Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, atualizou um Balancer de Carga Padrão existente com uma configuração IP frontal IPv4 para uma configuração dual stack (IPv4 e IPv6). Também adicionou configurações iPv6 aos NICs dos VMs na piscina de backend. Para saber mais sobre o suporte iPv6 em redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
