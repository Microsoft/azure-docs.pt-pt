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
ms.openlocfilehash: b34d37fa79ccb8344fdacd99877403d61ba5f5c2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138913"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Podem ser atualizados os seguintes itens: 

Galeria de imagem partilhada:
- Descrição

definição de imagem:
- VCPUs recomendada
- Memória recomendada
- Descrição
- Data de vida de fim de

Versão da imagem:
- Contagem de réplicas regional
- Regiões de destino
- Exclusão de versão mais recente
- Data de vida de fim de

Atualizar a descrição do uso de uma galeria ([atualização do az sig](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Atualizar a descrição de uma definição de imagem com [atualização de definição de imagem de sig az](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Atualizar uma versão de imagem para adicionar uma região para replicar a usar [atualização de versão da imagem de sig az](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Esta alteração irá demorar algum tempo, como a imagem é replicada para a nova região.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Eliminar recursos

Tem de eliminar os recursos na ordem inversa, eliminando primeiro a versão da imagem. Depois de eliminar todas as versões de imagem, pode eliminar a definição de imagem. Depois de eliminar todas as definições de imagem, pode eliminar a galeria. 

Eliminar uma versão de imagem usando [versão de imagem do sig az eliminar](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminar uma definição de imagem com [eliminar definição de imagem do sig az](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Eliminar uma galeria de imagens com [az sig eliminar](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```