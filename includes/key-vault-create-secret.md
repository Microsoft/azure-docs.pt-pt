---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245060"
---
Vamos criar um segredo chamado **mySecret,** com um valor de **Sucesso!** Um segredo pode ser uma palavra-passe, uma cadeia de ligação SQL, ou qualquer outra informação que você precisa para manter seguro e disponível para a sua aplicação. 

Para adicionar um segredo ao seu recém-criado cofre chave, use o comando secreto Azure CLI [az keyvault:](/cli/azure/keyvault/secret#az-keyvault-secret-set)

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```