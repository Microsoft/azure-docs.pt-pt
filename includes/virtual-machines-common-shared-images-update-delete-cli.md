---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fe37b0c9dbc16520a0dcb0993236db2797da6b68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556882"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Os seguintes itens podem ser atualizados: 

Galeria de imagens partilhada:
- Descrição

Definição de imagem:
- VCPUs recomendados
- Memória recomendada
- Descrição
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Exclusão das últimas
- Data de fim de vida

Se planeia adicionar regiões réplicas, não elimine a imagem gerida pela fonte. A imagem gerida pela fonte é necessária para replicar a versão de imagem para regiões adicionais. 

Atualize a descrição de uma galeria utilizando[(atualização az sig](/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Atualize a descrição de uma definição de imagem utilizando [a atualização da definição de imagem az sig](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Atualize uma versão de imagem para adicionar uma região para replicar a [utilização da atualização da versão de imagem az sig](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Esta mudança levará algum tempo à medida que a imagem for replicada para a nova região.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Este exemplo mostra como utilizar a [atualização da versão de imagem az sig](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) para excluir que esta versão de imagem seja usada como a *imagem mais recente.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Este exemplo mostra como usar a [atualização de versão az sig](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) para incluir esta versão de imagem em ser considerada para a *imagem mais recente.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Eliminar recursos

Tem de apagar os recursos em ordem inversa, eliminando primeiro a versão de imagem. Depois de eliminar todas as versões de imagem, pode eliminar a definição de imagem. Depois de eliminar todas as definições de imagem, pode apagar a galeria. 

Elimine uma versão de imagem utilizando [a versão az sig de imagem delete](/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Elimine uma definição de imagem utilizando [a definição de imagem az sig delete](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Elimine uma galeria de imagens usando [az sig delete](/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```