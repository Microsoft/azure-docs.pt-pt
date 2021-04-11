---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967132"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```