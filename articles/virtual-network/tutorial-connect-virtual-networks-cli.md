---
title: Ligar redes virtuais com o peering de rede virtual - CLI do Azure | Documentos da Microsoft
description: Neste artigo, saiba como ligar redes virtuais com a rede virtual peering, com a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
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
ms.openlocfilehash: f8ffd321d6f49b3d31c2e0119e14538409812680
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713070"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Ligar redes virtuais com o peering de rede virtual com a CLI do Azure

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a que está a executar a CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, terá de criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual denominada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Criar uma rede virtual denominada *myVirtualNetwork2* com o prefixo de endereço *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

Peerings são estabelecidos entre IDs de rede virtual, portanto, tem primeiro de obter o ID de cada rede virtual com [show de vnet de rede de az](/cli/azure/network/vnet) e armazenar a identificação numa variável.

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

Cria um peering a partir *myVirtualNetwork1* ao *myVirtualNetwork2* com [criar peering de vnet de rede de az](/cli/azure/network/vnet/peering). Se o `--allow-vnet-access` parâmetro não for especificado, um peering ser estabelecido, mas não há qualquer comunicação pode fluir através do mesmo.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Na saída devolvida após o comando anterior é executado, verá que o **peeringState** é *iniciado*. O peering permanece no *iniciado* até que cria o peering a partir de estado *myVirtualNetwork2* para *myVirtualNetwork1*. Cria um peering a partir *myVirtualNetwork2* ao *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Na saída devolvida após o comando anterior é executado, verá que o **peeringState** é *ligado*. Azure também alterou o estado do peering do *myVirtualNetwork1-myVirtualNetwork2* peering para *ligado*. Confirme que o estado do peering para o *myVirtualNetwork1-myVirtualNetwork2* peering alterado para *ligado* com [az network vnet show de peering](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Recursos de uma rede virtual não consegue comunicar com os recursos em outra rede virtual até que o **peeringState** para os peerings em ambas as redes virtuais é *ligado*. 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVm1* no *myVirtualNetwork1* rede virtual. Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. O `--no-wait` opção cria a VM em segundo plano, para poder continuar para o passo seguinte.

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

Criar uma VM no *myVirtualNetwork2* rede virtual.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Depois da VM é criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```azurecli 
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

Anote o **publicIpAddress**. Este endereço é utilizado para aceder à VM a partir da internet num passo posterior.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Utilize o seguinte comando para criar uma sessão SSH com o *myVm2* VM. Substitua `<publicIpAddress>` com o endereço IP público da sua VM. No exemplo anterior, é o endereço IP público *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Enviar um ping a VM na *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Receberá quatro respostas. 

Feche a sessão SSH para o *myVm2* VM. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [eliminação do grupo de az](/cli/azure/group) para remover o grupo de recursos e todos os recursos nele contidos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a ligar duas redes na mesma região do Azure, com o peering de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#cli), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN e interagir com os recursos numa rede virtual, ou em redes virtuais em modo de peering. Para obter scripts reutilizáveis concluir a muitas das tarefas abordadas os artigos de rede virtual, consulte [exemplos de scripts](cli-samples.md).
