---
title: Configure preferência de encaminhamento para um VM - Azure CLI
description: Saiba como criar um VM com um endereço IP público com escolha de preferência de encaminhamento utilizando a interface de linha de comando Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764482"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Configure a preferência de encaminhamento para um VM utilizando o Azure CLI

Este artigo mostra-lhe como configurar a preferência de encaminhamento para uma máquina virtual. O tráfego ligado à Internet a partir do VM será encaminhado através da rede ISP quando escolher a **Internet** como a sua opção de preferência de encaminhamento . O encaminhamento padrão é através da rede global da Microsoft.

Este artigo mostra-lhe como criar uma máquina virtual com um IP público que está definido para encaminhar o tráfego através da internet pública usando O Azure CLI.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Se utilizar a Cloud Shell, salte para o passo 2. Abra uma sessão de comando e assine em Azure com `az login` .
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos na região de Azure oriental dos EUA:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder às suas máquinas virtuais a partir da Internet, precisa de criar um endereço IP público. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria um ip público de linhagem de preferência *tipo Internet* na região leste *dos EUA:*

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Criar recursos de rede

Antes de implementar um VM, tem de criar recursos de rede de suporte - grupo de segurança de rede, rede virtual e NIC virtual.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que regem a comunicação de entrada e saída no seu VNet com [a rede az nsg criar](/cli/azure/network/nsg#az_network_nsg_create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo a seguir cria uma rede virtual chamada *myVNET* com sub-redes *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Criar um NIC

Crie um NIC virtual para o VM com [a criação de rede Az.](/cli/azure/network/nic#az_network_nic_create) O exemplo a seguir cria um NIC virtual, que será anexado ao VM.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria um VM do servidor windows 2019 e os componentes de rede virtual necessários se ainda não existirem.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos.](routing-preference-overview.md)
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre [as definições de endereço IP público .](virtual-network-public-ip-address.md#create-a-public-ip-address)
