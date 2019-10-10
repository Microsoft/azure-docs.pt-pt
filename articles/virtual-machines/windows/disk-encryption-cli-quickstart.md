---
title: Criar e criptografar uma VM do Windows com CLI do Azure
description: Neste guia de início rápido, você aprende a usar CLI do Azure para criar e criptografar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 9bbe74bd2f3137443b4e239201c604d9de52582e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246068"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Início rápido: criar e criptografar uma VM do Windows com o CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido mostra como usar o CLI do Azure para criar e criptografar uma VM (máquina virtual) do Windows Server 2016.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este guia de início rápido requer a execução da versão 2.0.30 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Key Vault configurado para chaves de criptografia

A criptografia de disco do Azure armazena sua chave de criptografia em um Azure Key Vault. Crie um Key Vault com [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Para habilitar o Key Vault para armazenar as chaves de criptografia, use o parâmetro--Enabled-for-Disk-Encryption.
> [!Important]
> Cada Key Vault deve ter um nome exclusivo. O exemplo a seguir cria um Key Vault chamado *myKV*, mas você deve nomear algo diferente.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

Criptografe sua VM com [AZ VM Encryption](/cli/azure/vm/encryption?view=azure-cli-latest), fornecendo seu nome de Key Vault exclusivo para o parâmetro--Disk-Encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Você pode verificar se a criptografia está habilitada em sua VM com [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Você verá o seguinte na saída retornada:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [AZ Group Delete](/cli/azure/group) para remover o grupo de recursos, a VM e o Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma máquina virtual, criou uma Key Vault que foi habilitada para chaves de criptografia e criptografou a VM.  Avance para o artigo seguinte para saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs de IaaS.

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)

