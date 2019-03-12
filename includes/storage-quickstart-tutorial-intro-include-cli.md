---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786452"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para fins gerais com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificar as credenciais da conta de armazenamento

O Azure CLI precisa das credenciais da conta de armazenamento para a maioria dos comandos neste tutorial. Apesar de existirem várias opções para o fazer, uma das formas mais simples de as fornecer consiste em definir as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`.

Em primeiro lugar, apresente as chaves da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Em seguida, defina as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`. Pode fazer isto na shell de deteção com o comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
