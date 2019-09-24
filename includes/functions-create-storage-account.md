---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203164"
---
## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As Funções utilizam uma conta de Armazenamento do Microsoft Azure para fins gerais, para guardar as informações de estado das funções, entre outras. Crie uma conta de armazenamento para fins gerais no grupo de recursos que criou, ao utilizar o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create).

No comando seguinte, substitua o nome da conta de armazenamento globalmente único onde vir o marcador de posição `<storage_name>`. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
