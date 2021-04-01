---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070287"
---
Utilize o teclado Azure CLI [az create](/cli/azure/keyvault#az_keyvault_create) command para criar um Cofre chave no grupo de recursos a partir do passo anterior. Terá de fornecer algumas informações:

- Nome do cofre chave: Uma sequência de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)

  > [!Important]
  > Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

A saída deste comando mostra propriedades do recém-criado cofre-chave. Tome nota das duas propriedades listadas abaixo:

- **Nome do cofre**: O nome que forneceu ao parâmetro de nome acima.
- **Cofre URI**: No exemplo, este é https:// &lt; o seu nome único de keyvault &gt; .vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.
