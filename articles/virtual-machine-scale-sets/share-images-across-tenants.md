---
title: Partilhe imagens da galeria através dos inquilinos
description: Aprenda a criar conjuntos de escala usando imagens que são partilhadas através de inquilinos do Azure usando Galerias de Imagem Partilhada.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b6b5e48997da03c7f180c62a9142c99b05c95b46
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444042"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Partilhar imagens entre inquilinos com Galeria de Imagens Partilhadas

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Criar um conjunto de dimensionamento com a CLI do Azure

Inscreva-se no principal de serviço para inquilino 1 usando o appID, a chave da aplicação, e o ID do inquilino 1. Pode usar `az account show --query "tenantId"` para obter as identificações do inquilino, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inscreva-se no principal de serviço para inquilino 2 usando o appID, a chave de aplicação, e o ID do inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie o conjunto de escala. Substitua a informação no exemplo por outra.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos seguintes

Se encontrar algum problema, pode [resolver as galerias de imagens partilhadas.](../virtual-machines/troubleshooting-shared-images.md)
