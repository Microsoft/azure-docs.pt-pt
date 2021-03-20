---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89482144"
---
1. Utilize o `az group create` comando para criar um grupo de recursos:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Pode mudar o "Eastus" para um local mais próximo de si, se preferir.

1. Utilize `az keyvault create` para criar o cofre da chave:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Substitua `<your-unique-keyvault-name>` por um nome único em todo o Azure. Normalmente, usa o seu nome pessoal ou da empresa juntamente com outros números e identificadores. 

1. Crie uma variável ambiental que forneça o nome do Cofre chave ao código:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[festa](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
