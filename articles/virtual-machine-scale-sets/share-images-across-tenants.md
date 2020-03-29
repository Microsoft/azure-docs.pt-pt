---
title: Partilhe imagens de galerias entre inquilinos em Azure
description: Saiba como partilhar imagens vm em inquilinos do Azure usando Galerias de Imagem Partilhada.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276340"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partilhar imagens vM da galeria através dos inquilinos do Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Criar um conjunto de dimensionamento com a CLI do Azure

Inscreva-se no principal de serviço para o inquilino 1 usando o appID, a chave da aplicação e a identificação do inquilino 1. Pode usar `az account show --query "tenantId"` para obter as identificações dos inquilinos, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inscreva-se no principal de serviço para inquilino 2 usando o appID, a chave da aplicação e a identificação do inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie o conjunto de escala. Substitua a informação no exemplo com a sua.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema, pode resolver as galerias de [imagens partilhadas.](troubleshooting-shared-images.md)
