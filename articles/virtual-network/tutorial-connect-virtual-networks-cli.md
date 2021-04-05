---
title: Ligue redes virtuais com o estomo VNet - Azure CLI
description: Neste artigo, aprende-se a ligar redes virtuais com o espreitamento de rede virtual, utilizando o CLI Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741795"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Conecte redes virtuais com o espreitamento da rede virtual utilizando o Azure CLI

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Criar uma rede virtual chamada *myVirtualNetwork2* com o prefixo de endereço *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

Os persitos são estabelecidos entre iDs de rede virtual, por isso primeiro deve obter o ID de cada rede virtual com [a série de vnet de rede az](/cli/azure/network/vnet) e armazenar o ID numa variável.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Crie um espreitamento do *myVirtualNetwork1* para *o myVirtualNetwork2* com [a az network vnet peering create](/cli/azure/network/vnet/peering). Se o `--allow-vnet-access` parâmetro não for especificado, é estabelecido um espreitêrão, mas nenhuma comunicação pode fluir através dele.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado de observação** é *Iniciado*. O espreitamento permanece no estado *iniciado* até criar o espreitamento do *myVirtualNetwork2* para *o myVirtualNetwork1*. Crie um espreitante do *myVirtualNetwork2* para *o myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado-Maior está** *ligado*. O Azure também alterou o estado de observação do *myVirtualNetwork1-myVirtualNetwork2* olhando para *Connected*. Confirme que o estado de observação do *myVirtualNetwork1-myVirtualNetwork2* mudou para *Connected* with [az network vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Os recursos numa rede virtual não podem comunicar com recursos na outra rede virtual até que o **Estado de observação** para os seus pares em ambas as redes virtuais esteja *conectado*. 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria um VM chamado *myVm1* na rede virtual *myVirtualNetwork1.* Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. A `--no-wait` opção cria o VM em segundo plano, para que possa continuar até ao passo seguinte.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Crie um VM na rede virtual *myVirtualNetwork2.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Após a criação do VM, o Azure CLI mostra informações semelhantes ao seguinte exemplo: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Utilize o seguinte comando para criar uma sessão de SSH com o *myVm2* VM. `<publicIpAddress>`Substitua-o pelo endereço IP público do seu VM. No exemplo anterior, o endereço IP público é *de 13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Ping o VM na *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Recebes quatro respostas. 

Feche a sessão SSH ao *myVm2* VM. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize [o grupo AZ para remover](/cli/azure/group) o grupo de recursos e todos os recursos que contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar duas redes na mesma região do Azure, com olhando para a rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#cli), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN e interagir com recursos numa rede virtual, ou em redes virtuais. Para scripts reutilizáveis para completar muitas das tarefas abrangidas pelos artigos de rede virtual, consulte [as amostras de script](cli-samples.md).
