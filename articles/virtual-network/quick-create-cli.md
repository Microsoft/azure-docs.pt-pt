---
title: Criar uma rede virtual - quickstart - Azure CLI
titlesuffix: Azure Virtual Network
description: Neste arranque rápido, aprenda a criar uma rede virtual utilizando o Azure CLI. Uma rede virtual permite que os recursos do Azure comuniquem uns com os outros e com a internet.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056793"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início Rápido: criar uma rede virtual com a CLI do Azure

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem-se em privado entre si e com a internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecta-se aos VMs a partir da internet e comunica-se em privado através da nova rede virtual.
## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 ou posterior do Azure CLI. Para encontrar a sua versão instalada, corra `az --version` . Consulte [o Azure CLI](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para acolher a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *leste:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *padrão*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). Se as teclas SSH já não existirem num local de chave predefinido, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte. Este exemplo cria um VM chamado *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Uma vez que usou a `--no-wait` opção no passo anterior, pode ir em frente e criar o segundo VM chamado *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Mensagem de saída do Azure CLI

A criação das VMs demora alguns minutos. Depois de Azure criar os VMs, o Azure CLI devolve a saída desta forma:

```output
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

Anote o **publicIpAddress**. Utilizará este endereço para ligar ao VM a partir da internet no passo seguinte.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Neste comando, `<publicIpAddress>` substitua-o pelo endereço IP público do seu *myVm2* VM:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Para confirmar a comunicação privada entre o *myVm2* e *o myVm1* VMs, insira este comando:

```bash
ping myVm1 -c 4
```

Receberá quatro respostas a partir de *10.0.0.4*.

Saia da sessão SSH com a VM *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [o grupo AZ para remover](/cli/azure/group) o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligou-se a um VM da internet e comunicou-se em privado entre os dois VMs.
O Azure permite uma comunicação privada sem restrições entre os VMs. Por predefinição, o Azure só permite ligações remotas de ambiente de trabalho ao Windows VMs a partir da internet. Avance para o próximo artigo para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM:
> [!div class="nextstepaction"]
> [Filtrar o tráfego de rede](tutorial-filter-network-traffic.md)
