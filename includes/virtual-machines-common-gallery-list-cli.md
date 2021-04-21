---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800091"
---
## <a name="list-information"></a>Informação da lista

Obtenha a localização, estado e outras informações sobre as galerias de imagem disponíveis usando [a lista az sig](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Listar as definições de imagem numa galeria, incluindo informações sobre o tipo e o estado do SO, utilizando [a lista de definição de imagem az sig](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Listar as versões de imagem partilhadas numa galeria, utilizando [a lista de versão az sig.](/cli/azure/sig/image-version#az_sig_image_version_list)

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenha o ID de uma versão de imagem usando [a az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
