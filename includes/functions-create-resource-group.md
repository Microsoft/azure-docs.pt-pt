---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203135"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Function App, bases de dados e contas de armazenamento, são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.  
Se você não estiver usando Cloud Shell, entre primeiro usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Em geral, você cria seu grupo de recursos e os recursos em uma [região](https://azure.microsoft.com/global-infrastructure/regions/) perto de você. 
