---
title: Partilhe imagens da galeria através dos inquilinos
description: Saiba como partilhar imagens VM através de inquilinos Azure usando Galerias de Imagem Partilhada usando Azure CLI.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ef788659c871a9bcef6f519664689eacda94daa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552886"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Partilhar imagens da galeria VM através dos inquilinos do Azure usando o CLI Azure

As Galerias de Imagem Partilhadas permitem-lhe partilhar imagens usando o Azure RBAC. Você pode usar a Azure RBAC para compartilhar imagens dentro do seu inquilino, e até mesmo para indivíduos fora do seu inquilino. Para mais informações sobre esta simples opção de partilha, consulte a [galeria Partilhar.](./shared-images-portal.md#share-the-gallery)

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Não é possível utilizar o portal para implantar um VM a partir de uma imagem em outro inquilino azul. Para criar um VM a partir de uma imagem partilhada entre inquilinos, você deve usar o Azure CLI ou [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Criar um VM utilizando o Azure CLI

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

Criar o VM. Substitua a informação no exemplo por outra.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos seguintes

Se encontrar algum problema, pode [resolver as galerias de imagens partilhadas.](../troubleshooting-shared-images.md)
