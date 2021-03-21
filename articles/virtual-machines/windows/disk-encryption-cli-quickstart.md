---
title: Criar e encriptar uma VM do Windows com a CLI do Azure
description: Neste arranque rápido, aprende-se a usar o CLI do Azure para criar e encriptar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: bffa155a51c4897cd1e2cc07a6d4aea9a8e080ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562763"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Quickstart: Criar e encriptar um VM do Windows com o Azure CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o CLI Azure para criar e encriptar uma máquina virtual do Windows Server 2016 (VM).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.30 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM*. Este exemplo utiliza *azureuser* para um nome de utilizador administrativo e *myPassword12* como palavra-passe.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte resultado de exemplo mostra que a operação de criação da VM foi concluída com êxito.

```console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Cofre de Chaves configurado para chaves de encriptação

A encriptação do disco Azure armazena a sua chave de encriptação num Cofre de Chaves Azure. Crie um Cofre chave com [criação de keyvault az](/cli/azure/keyvault#az-keyvault-create). Para ativar o Cofre de Chaves para armazenar chaves de encriptação, utilize o parâmetro de encriptação ativado para o disco.
> [!Important]
> Cada Cofre-Chave deve ter um nome único. O exemplo a seguir cria um Key Vault chamado *myKV,* mas deve nomear o seu algo diferente.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

Criptografe o seu VM com [encriptação az vm,](/cli/azure/vm/encryption)fornecendo o seu nome único de Key Vault para o parâmetro --disk-encriptação-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Pode verificar se a encriptação está ativada no seu VM com [a az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

Verá o seguinte na saída devolvida:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, VM e Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma máquina virtual, criou um Key Vault que permitia chaves de encriptação e encriptava o VM.  Avance para o artigo seguinte para saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs de IaaS.

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
