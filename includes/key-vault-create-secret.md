---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512819"
---
Vamos criar um segredo chamado **mySecret,** com um valor de **Sucesso!** Um segredo pode ser uma palavra-passe, uma cadeia de ligação SQL, ou qualquer outra informação que você precisa para manter seguro e disponível para a sua aplicação. 

Para adicionar um segredo ao seu recém-criado cofre chave, use o comando secreto Azure CLI [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```