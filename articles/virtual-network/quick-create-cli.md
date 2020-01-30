---
title: Criar uma rede virtual - quickstart - Azure CLI
titlesuffix: Azure Virtual Network
description: Neste arranque rápido, aprende-se a criar uma rede virtual utilizando o Azure CLI. Uma rede virtual permite que os recursos do Azure, como máquinas virtuais, comuniquem-se em privado uns com os outros, e com a internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 3cbfee90997c6b7cd9df1ec76543d77a4402100f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774525"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início Rápido: criar uma rede virtual com a CLI do Azure

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem em privado uns com os outros, e com a internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecta-se aos VMs a partir da internet e comunica-se em privado através da nova rede virtual.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar CLI do Azure localmente, este guia de início rápido exigirá que você use CLI do Azure versão 2.0.28 ou posterior. Para localizar sua versão instalada, execute `az --version`. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Para poder criar uma rede virtual, você precisa criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma subnet chamada *padrão:*

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). Se as teclas SSH já não existirem num local de chave padrão, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte. Este exemplo cria um VM chamado *myVm1:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Uma vez que usou a opção `--no-wait` no passo anterior, pode ir em frente e criar o segundo VM chamado *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Mensagem de saída Azure CLI

A criação das VMs demora alguns minutos. Depois de o Azure criar os VMs, o Azure CLI devolve a saída desta forma:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Anote o **publicIpAddress**. Você usará esse endereço para se conectar à VM da Internet na próxima etapa.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Neste comando, substitua `<publicIpAddress>` pelo endereço IP público do seu *myVm2* VM:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Para confirmar a comunicação privada entre os *MyVm2* e *myVm1* VMs, insira este comando:

```bash
ping myVm1 -c 4
```

Receberá quatro respostas a partir das *10.0.0.4*.

Saia da sessão SSH com a VM *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o [grupo AZ eliminar](/cli/azure/group) para remover o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligou-se a um VM da internet e comunicou-se em privado entre os dois VMs. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

Azure permite uma comunicação privada sem restrições entre VMs. Por predefinição, o Azure apenas permite ligar a entrada remota de desktop para VMs windows a partir da internet. Para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM, vá ao tutorial de tráfego da [rede Filter.](tutorial-filter-network-traffic.md)
