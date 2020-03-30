---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226034"
---
## <a name="using-rbac-to-share-images"></a>Usando o RBAC para partilhar imagens

Pode partilhar imagens através de subscrições utilizando o Controlo de Acesso Baseado em Funções (RBAC). Qualquer utilizador que tenha lido permissões para uma versão de imagem, mesmo através de subscrições, será capaz de implementar uma Máquina Virtual utilizando a versão de imagem.

Para obter mais informações sobre como partilhar recursos usando o RBAC, consulte [Gerir o acesso utilizando o RBAC e o Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informações de lista

Obtenha a localização, estado e outras informações sobre as galerias de imagem disponíveis usando a [lista az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Enumera rine as definições de imagem numa galeria, incluindo informações sobre o tipo de OS e o estado, utilizando a [lista de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Liste as versões de imagem partilhada numa galeria, utilizando a [lista de versão de imagem az sig](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenha a identificação de uma versão de imagem usando [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```