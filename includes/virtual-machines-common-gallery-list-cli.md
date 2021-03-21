---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6432c125c4fedd962faa28a4c84c7494300b0472
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755389"
---
## <a name="list-information"></a>Informação da lista

Obtenha a localização, estado e outras informações sobre as galerias de imagem disponíveis usando [a lista az sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Listar as definições de imagem numa galeria, incluindo informações sobre o tipo e o estado do SO, utilizando [a lista de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Listar as versões de imagem partilhadas numa galeria, utilizando [a lista de versão az sig.](/cli/azure/sig/image-version#az-sig-image-version-list)

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenha o ID de uma versão de imagem usando [a az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
