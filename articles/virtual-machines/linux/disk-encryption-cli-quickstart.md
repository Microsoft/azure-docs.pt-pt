---
title: Crie e criptografe um Linux VM com O CLI Azure
description: Neste arranque rápido, aprende-se a usar o Azure CLI para criar e encriptar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 14b6f07ead59f3d86b80489460956dd37fb7378b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970700"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Quickstart: Crie e criptografe um VM Linux com o Azure CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o Azure CLI para criar e encriptar uma máquina virtual Linux (VM).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e utilizar a CLI localmente, este guia de início rápido requer a execução da versão 2.0.30 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com o comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte resultado de exemplo mostra que a operação de criação da VM foi concluída com êxito.

```azurecli-interactive
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Cofre chave configurado para chaves de encriptação

A encriptação do disco Azure armazena a sua chave de encriptação num Cofre de Chaves Azure. Crie um Cofre chave com [az keyvault criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Para permitir que o Cofre chave guarde as teclas de encriptação, utilize o parâmetro de encriptação ativado por disco.

> [!Important]
> Todos os cofres devem ter um nome único em Azure. Nos exemplos abaixo, substitua <your-unique-keyvault-name> com o nome que escolher.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

Criptografe o seu VM com [encriptação az vm,](/cli/azure/vm/encryption?view=azure-cli-latest)fornecendo o seu nome exclusivo do Cofre chave para o parâmetro --encriptação-chave-chave.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Após um momento o processo vai voltar, "O pedido de encriptação foi aceite. Por favor, use o comando 'show' para monitorizar o progresso." O comando "show" é [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Quando a encriptação estiver ativada, verá o seguinte na saída devolvida:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos, VM e Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma máquina virtual, criou um Cofre chave que estava habilitado para chaves de encriptação e encriptava o VM.  Avance para o próximo artigo para saber mais sobre mais encriptação de disco azure para VMs Linux.

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco azure](disk-encryption-overview.md)