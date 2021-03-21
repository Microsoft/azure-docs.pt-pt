---
title: Criar e encriptar uma VM do Linux com a CLI do Azure
description: Neste arranque rápido, você aprende a usar O CLI Azure para criar e encriptar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b600c895f98c46dbaafce7deef86bbbee8f57c99
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561046"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Quickstart: Criar e encriptar um Linux VM com o Azure CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o CLI Azure para criar e encriptar uma máquina virtual Linux (VM).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e utilizar o Azure CLI localmente, este arranque rápido requer que esteja a executar a versão Azure CLI 2.0.30 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo seguinte cria uma VM com o nome *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte resultado de exemplo mostra que a operação de criação da VM foi concluída com êxito.

```
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

A encriptação do disco Azure armazena a sua chave de encriptação num Cofre de Chaves Azure. Crie um Cofre chave com [criação de keyvault az](/cli/azure/keyvault#az_keyvault_create). Para ativar o Cofre de Chaves para armazenar chaves de encriptação, utilize o parâmetro de encriptação ativado para o disco.

> [!Important]
> Cada cofre deve ter um nome único em Azure. Nos exemplos abaixo, substitua <o seu nome único> pelo nome que escolher.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

Criptografe o seu VM com [encriptação az vm,](/cli/azure/vm/encryption)fornecendo o seu nome único de Key Vault para o parâmetro --disk-encriptação-keyvault.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Após um momento, o processo regressará, "O pedido de encriptação foi aceite. Por favor, use o comando 'show' para monitorizar o progresso." O comando "show" é [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Quando a encriptação estiver ativada, verá o seguinte na saída devolvida:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, VM e Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma máquina virtual, criou um Key Vault que permitia chaves de encriptação e encriptava o VM.  Avance para o próximo artigo para saber mais sobre mais Encriptação de Disco Azure para Os VMs Linux.

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
