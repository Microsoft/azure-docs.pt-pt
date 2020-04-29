---
title: Conecte redes virtuais com peering VNet - Azure CLI
description: Neste artigo, aprende-se a ligar redes virtuais com o peering virtual da rede, utilizando o Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: aa2d75173b14e768a207336b54b3dc10a8c3ea5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235154"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Conecte redes virtuais com o peering de rede virtual utilizando o Azure CLI

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Criar uma rede virtual chamada *myVirtualNetwork2* com o prefixo de endereço *10.1.0.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

Os pares são estabelecidos entre iDs de rede virtual, por isso você deve primeiro obter a identificação de cada rede virtual com [az network vnet show](/cli/azure/network/vnet) e armazenar o ID em uma variável.

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

Crie um peering do *myVirtualNetwork1* para *myVirtualNetwork2* com a [rede az vnet peering criar](/cli/azure/network/vnet/peering). Se `--allow-vnet-access` o parâmetro não for especificado, estabelece-se um especio, mas nenhuma comunicação pode fluir através dele.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado de pares** é *iniciado*. O peering permanece no estado *iniciado* até criar o peering do *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um olhar do *myVirtualNetwork2* para *o myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado peering** está *ligado*. O Azure também mudou o estado de peering do *myVirtualNetwork1-myVirtualNetwork2* peering to *Connected*. Confirme que o estado de observação do *myVirtualNetwork1-myVirtualNetwork2 alterou-se* para *Connected* with [az network vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Os recursos de uma rede virtual não podem comunicar com recursos na outra rede virtual até que o Estado de **pares** para os pares em ambas as redes virtuais esteja *conectado.* 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria um VM chamado *myVm1* na rede virtual *myVirtualNetwork1.* Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. A `--no-wait` opção cria o VM em segundo plano, para que possa continuar até ao próximo passo.

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

Utilize o seguinte comando para criar uma sessão SSH com o *myVm2* VM. Substitua `<publicIpAddress>` pelo endereço IP público do seu VM. No exemplo anterior, o endereço IP público é *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Ping o VM no *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Recebequatro respostas. 

Feche a sessão sSH ao *myVm2* VM. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [grupo Az eliminar](/cli/azure/group) para remover o grupo de recursos e todos os recursos que contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar duas redes na mesma região de Azure, com o peering de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#cli), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN, e interagir com recursos numa rede virtual ou em redes virtuais. Para que scripts reutilizáveis completem muitas das tarefas abrangidas pelos artigos de rede virtuais, consulte [amostras](cli-samples.md)de script .
