---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512819"
---
Vamos criar um segredo chamado **mySecret,** com um valor de **Sucesso!** Um segredo pode ser uma palavra-passe, uma cadeia de ligação SQL, ou qualquer outra informação que você precisa para manter seguro e disponível para a sua aplicação. 

Para adicionar um segredo ao seu recém-criado cofre chave, use o comando secreto Azure CLI [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```