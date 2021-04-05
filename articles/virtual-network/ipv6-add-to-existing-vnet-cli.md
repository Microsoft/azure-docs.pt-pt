---
title: Adicione IPv6 a uma aplicação IPv4 na rede virtual Azure - Azure CLI
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 para uma aplicação existente na rede virtual Azure usando O Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932608"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Adicione IPv6 a uma aplicação IPv4 na rede virtual Azure - Azure CLI

Este artigo mostra-lhe como adicionar endereços IPv6 a uma aplicação que está a usar o endereço IP público IPv4 numa rede virtual Azure para um Balanceador de Carga Padrão utilizando o Azure CLI. A atualização no local inclui uma rede virtual e sub-rede, um Balanceador de Carga Standard com configurações frontend IPv4 + IPV6, VMs com NICs que têm configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.

## <a name="prerequisites"></a>Pré-requisitos

- Este artigo pressupõe que implementou um Balanceador de Carga Standard como descrito no [Quickstart: Criar um Balanceador de Carga Padrão - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-ipv6-addresses"></a>Criar endereços IPv6

Crie o endereço público IPv6 com [a rede Az public-ip criar](/cli/azure/network/public-ip) para o seu Balancer de Carga Padrão. O exemplo a seguir cria um endereço IP público IPv6 nomeado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB:*

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configurar iPv6 carregar frontend

Configure o balançador de carga com o novo endereço IP IPv6 utilizando [a rede az-rede lb frontend-ip criar](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) da seguinte forma:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configure piscina de backend de balançador de carga IPv6

Crie o pool backend para NICs com endereços IPv6 utilizando [o pool de endereços lb da rede az criar](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) da seguinte forma:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configurar regras do balançador de carga IPv6

Criar regras de balançador de carga IPv6 com [regra lb de rede az criar](/cli/azure/network/lb/rule#az-network-lb-rule-create).

```azurecli-interactive
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

## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicione intervalos de endereços IPv6 à rede virtual e sub-rede que hospeda o balançador de carga da seguinte forma:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Adicionar configuração IPv6 aos NICs

Configure os NICs VM com um endereço IPv6 utilizando [a rede az nic ip-config criar](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) da seguinte forma:

```azurecli-interactive
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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de pilha dupla IPv6 no portal Azure

Pode ver a rede virtual de pilha dupla IPv6 no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, insira *o myVnet*.
2. Quando **o myVnet** aparecer nos resultados da pesquisa, selecione-o. Isto lança a página **geral** da rede virtual dual stack chamada *myVNet*. A rede virtual dual stack mostra os três NICs com configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mySubnet*.

  ![IPv6 dual stack rede virtual em Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, atualizou um Balancer de Carga Standard existente com uma configuração IP frontal IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Também adicionou configurações IPv6 aos NICs dos VMs na piscina de backend. Para saber mais sobre o suporte do IPv6 nas redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
