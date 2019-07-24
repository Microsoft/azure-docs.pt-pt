---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444138"
---
## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As Funções utilizam uma conta de Armazenamento do Microsoft Azure para fins gerais, para guardar as informações de estado das funções, entre outras. Crie uma conta de armazenamento para fins gerais no grupo de recursos que criou, ao utilizar o comando [az storage account create](/cli/azure/storage/account).

No comando seguinte, substitua o nome da conta de armazenamento globalmente único onde vir o marcador de posição `<storage_name>`. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
