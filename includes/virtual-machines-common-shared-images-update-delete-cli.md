---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184271"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações no que pode ser atualizado. Os seguintes itens podem ser atualizados: 

Galeria de imagens partilhadas:
- Descrição

Definição de imagem:
- VCPUs recomendado
- Memória recomendada
- Descrição
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Exclusão dos últimos
- Data de fim de vida

Se pretender adicionar réplicas de regiões, não apague a imagem gerida pela fonte. A imagem gerida pela fonte é necessária para replicar a versão de imagem para regiões adicionais. 

Atualize a descrição de uma galeria utilizando[(az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Atualize a descrição de uma definição de imagem utilizando [aatualização de definição de imagem az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Atualize uma versão de imagem para adicionar uma região para replicar a utilização da atualização da [versão de imagem az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Esta mudança levará algum tempo à medida que a imagem for replicada para a nova região.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Eliminar recursos

Tem de apagar recursos por ordem inversa, eliminando primeiro a versão da imagem. Depois de eliminar todas as versões de imagem, pode eliminar a definição de imagem. Depois de eliminar todas as definições de imagem, pode apagar a galeria. 

Elimine uma versão de imagem utilizando a [versão de imagem az sig](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminar uma definição de imagem utilizando a [definição de imagem az sig eliminar](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Elimine uma galeria de imagens utilizando [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
