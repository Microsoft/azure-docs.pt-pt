---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793702"
---
## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso utilizado para permitir a partilha de imagens. 

Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens utilizando [a az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria um grupo de recursos chamado *myGalleryRG* in *East US,* e uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Partilhe a galeria

Pode partilhar imagens através de subscrições utilizando Role-Based Access Control (RBAC). Pode partilhar imagens na galeria, definição de imagem ou nível de versão de imagem. Qualquer utilizador que tenha lido permissões para uma versão de imagem, mesmo através de subscrições, poderá implementar um VM utilizando a versão de imagem.

Recomendamos que partilhe com outros utilizadores ao nível da galeria. Para obter a identificação do objeto da sua galeria, use [a az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use o ID do objeto como um âmbito, juntamente com um endereço de e-mail e [uma atribuição de funções az](/cli/azure/role/assignment#az-role-assignment-create) criar para dar a um utilizador acesso à galeria de imagens partilhada. Substitua `<email-address>` e pela sua própria `<gallery iD>` informação.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações sobre como partilhar recursos usando o RBAC, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
