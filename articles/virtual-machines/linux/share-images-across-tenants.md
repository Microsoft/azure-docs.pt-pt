---
title: Partilhar imagens da galeria em inquilinos no Azure | Documentos da Microsoft
description: Saiba como partilhar imagens de VM em inquilinos do Azure, galerias de imagem partilhada a utilizar.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 1578ba840c6dca93feb68754863439811d7ef099
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158734"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partilhar imagens de VM de galeria em inquilinos do Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-azure-cli"></a>Criar uma VM com a CLI do Azure

Inicie sessão no principal de serviço para o inquilino com o appID, a chave da aplicação e o ID do inquilino 1 de 1. Pode usar `az account show --query "tenantId"` para obter IDs de inquilino se for necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inicie sessão no principal de serviço para o inquilino com o appID, a chave da aplicação e o ID do inquilino 2 de 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie a VM. Substitua as informações no exemplo pelos seus próprios.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos Seguintes

Caso se depare com quaisquer problemas, pode [resolver problemas de galerias de imagem partilhada](troubleshooting-shared-images.md).