---
title: Criar e encriptar uma VM do Windows com a CLI do Azure
description: Neste arranque rápido, aprende-se a usar o Azure CLI para criar e encriptar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: aa0dd0bf55d51800d6a9b4283aa9e653887bf2f1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082803"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Quickstart: Criar e encriptar um VM windows com o Azure CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o AZURI CLI para criar e encriptar uma máquina virtual Do Windows Server 2016 (VM).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o Azure CLI localmente, este quickstart requer que esteja a executar a versão Azure CLI 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM*. Este exemplo utiliza *azureuser* para um nome de utilizador administrativo e *myPassword12* como palavra-passe.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Cofre chave configurado para chaves de encriptação

A encriptação do disco Azure armazena a sua chave de encriptação num Cofre de Chaves Azure. Crie um Cofre chave com [az keyvault criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Para permitir que o Cofre chave guarde as teclas de encriptação, utilize o parâmetro de encriptação ativado por disco.
> [!Important]
> Cada cofre de chaves deve ter um nome único. O exemplo seguinte cria um Cofre chave chamado *myKV,* mas deve nomear o seu algo diferente.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

Criptografe o seu VM com [encriptação az vm,](/cli/azure/vm/encryption?view=azure-cli-latest)fornecendo o seu nome exclusivo do Cofre chave para o parâmetro --encriptação-chave-chave.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Pode verificar se a encriptação está ativada no seu VM com [show az vm](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Verá o seguinte na saída devolvida:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos, VM e Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma máquina virtual, criou um Cofre chave que estava habilitado para chaves de encriptação e encriptava o VM.  Avance para o artigo seguinte para saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs de IaaS.

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco azure](disk-encryption-overview.md)
