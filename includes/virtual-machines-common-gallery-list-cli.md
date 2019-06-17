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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226034"
---
## <a name="using-rbac-to-share-images"></a>Utilizar o RBAC para partilhar imagens

Pode partilhar imagens entre subscrições através do controlo de acesso baseado em funções (RBAC). Qualquer utilizador que tem permissões de leitura para uma versão de imagem, até mesmo entre subscrições, será capaz de implantar uma Máquina Virtual utilizando a versão da imagem.

Para obter mais informações sobre como partilhar recursos com o RBAC, veja [gerir o acesso com RBAC e a CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Informações da lista

Obter a localização, estado e outras informações sobre as galerias de imagem disponíveis usando [lista de sig az](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Lista as definições de imagem numa galeria, incluindo informações sobre o tipo de SO e o estado, com [lista de definição de imagem de sig az](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Lista as versões de imagem partilhada numa galeria, usando [lista de versão da imagem de sig az](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obter o ID de uma versão de imagem usando [show de versão da imagem de sig az](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```