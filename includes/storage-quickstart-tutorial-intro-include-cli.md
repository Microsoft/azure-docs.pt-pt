---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747911"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para fins gerais com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas.

Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificar as credenciais da conta de armazenamento

O Azure CLI precisa das credenciais da conta de armazenamento para a maioria dos comandos neste tutorial. Apesar de existirem várias opções para o fazer, uma das formas mais simples de as fornecer consiste em definir as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`.

> [!NOTE]
> Este artigo mostra como definir variáveis de ambiente usando o bash. Outros ambientes podem exigir modificações de sintaxe.

Primeiro, exiba as chaves da conta de armazenamento usando o comando [AZ Storage Account Keys List](/cli/azure/storage/account/keys) . Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Em seguida, defina as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`. Você pode fazer isso no shell bash usando o comando `export`. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Para obter mais informações sobre como recuperar as chaves de acesso da conta usando o portal do Azure, consulte **chaves de acesso** em [gerenciar configurações da conta de armazenamento no portal do Azure](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys).