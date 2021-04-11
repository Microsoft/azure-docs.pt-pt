---
title: Criar uma rede virtual - quickstart - Azure CLI
titlesuffix: Azure Virtual Network
description: Neste arranque rápido, aprenda a criar uma rede virtual utilizando o Azure CLI. Uma rede virtual permite que os recursos do Azure comuniquem uns com os outros e com a internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0795404c2dc5377d60896863f6a088c4b2ffd1ad
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060827"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início Rápido: criar uma rede virtual com a CLI do Azure

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem-se em privado entre si e com a internet. 

Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecta-se aos VMs a partir da internet e comunica-se em privado através da nova rede virtual.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para acolher a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Este exemplo cria um grupo de recursos chamado **CreateVNetQS-rg** na localização **Eastus:**

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Este exemplo cria uma rede virtual padrão chamada **myVNet** com uma sub-rede chamada **padrão:**

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). 

Se as teclas SSH já não existirem num local de chave predefinido, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. 

A `--no-wait` opção cria o VM em segundo plano. Pode continuar até ao próximo passo. 

Este exemplo cria um VM chamado **myVM1**:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Usou a `--no-wait` opção no passo anterior. Pode ir em frente e criar o segundo VM chamado **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Mensagem de saída do Azure CLI

A criação das VMs demora alguns minutos. Depois de Azure criar os VMs, o Azure CLI devolve a saída desta forma:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>IP público VM

Para obter o endereço IP público **myVM2,** use [o show público-ip da rede Az:](/cli/azure/network/public-ip#az-network-public-ip-show)

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Neste comando, `<publicIpAddress>` substitua-o pelo endereço IP público do seu **myVM2** VM:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Para confirmar a comunicação privada entre o **myVM2** e **o myVM1** VMs, insira este comando:

```bash
ping myVM1 -c 4
```

Receberá quatro respostas a partir de *10.0.0.4*.

Saia da sessão SSH com o **myVM2** VM.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [o grupo AZ para remover](/cli/azure/group#az_group_delete) o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido: 

* Criou uma rede virtual padrão e dois VMs. 
* Ligou-se a um VM da internet e comunicou-se em privado entre os dois VMs.

A comunicação privada entre VMs é ilimitada numa rede virtual. 

Avance para o próximo artigo para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM:
> [!div class="nextstepaction"]
> [Filtre o tráfego de rede](tutorial-filter-network-traffic.md)
