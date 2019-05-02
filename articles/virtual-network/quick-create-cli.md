---
title: Criar uma rede virtual - início rápido - CLI do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com a CLI do Azure. Uma rede virtual permite que os recursos do Azure, como as máquinas virtuais, comuniquem em privado entre si e com a internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 6306d893f491f93cc31b7e478afe5632e997285c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692650"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início rápido: Criar uma rede virtual com a CLI do Azure

Uma rede virtual permite que os recursos do Azure, como as máquinas virtuais (VMs), comuniquem em privado entre si e com a internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, ligar às VMs a partir da internet e se comunicar em privado sobre a nova rede virtual.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente em vez disso, este início rápido requer a utilização da CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Ver [instalar a CLI do Azure](/cli/azure/install-azure-cli) para instalação ou atualização de informações.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Antes de poder criar uma rede virtual, tem de criar um grupo de recursos para alojar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* no *eastus* localização:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). Este exemplo cria uma rede de virtual predefinida com o nome *myVirtualNetwork* com uma sub-rede designada *padrão*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização chave predefinida, o comando cria-los. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte. Este exemplo cria uma VM com o nome *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Uma vez que utilizou o `--no-wait` opção no passo anterior, pode prosseguir e criar a segunda VM com o nome *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Mensagem de saída da CLI do Azure

A criação das VMs demora alguns minutos. Depois do Azure cria as VMs, a CLI do Azure devolve resultados como este:

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

Anote o **publicIpAddress**. Irá utilizar este endereço para ligar à VM a partir da internet no próximo passo.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Neste comando, substitua `<publicIpAddress>` com o endereço IP público do seu *myVm2* VM:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Para confirmar a comunicação privada entre o *myVm2* e *myVm1* VMs, introduzir este comando:

```bash
ping myVm1 -c 4
```

Receberá quatro respostas de *10.0.0.4*.

Saia da sessão SSH com a VM *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [eliminação do grupo de az](/cli/azure/group) para remover o grupo de recursos e todos os recursos de que tem:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligado a uma VM a partir da internet e comunicou em privado entre as duas VMs. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

O Azure permite a comunicação privada sem restrições entre VMs. Por predefinição, o Azure também permite apenas ligações de ambiente de trabalho remotas entrada às VMs do Windows da internet. Para saber mais sobre como configurar diferentes tipos de comunicações de rede VM, vá para o [filtrar o tráfego de rede](tutorial-filter-network-traffic.md) tutorial.
