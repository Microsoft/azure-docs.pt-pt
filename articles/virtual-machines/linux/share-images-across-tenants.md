---
title: Compartilhar imagens da Galeria entre locatários no Azure | Microsoft Docs
description: Saiba como compartilhar imagens de VM entre locatários do Azure usando galerias de imagens compartilhadas.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: b63bc1089b113edaac637df0a7e55c39f3a11f1a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904983"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Compartilhar imagens de VM da galeria em locatários do Azure

As galerias de imagens compartilhadas permitem compartilhar imagens usando o RBAC. Você pode usar o RBAC para compartilhar imagens dentro de seu locatário e até mesmo para indivíduos fora do seu locatário. Para obter mais informações sobre essa opção de compartilhamento simples, consulte [compartilhar a Galeria](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Você não pode usar o portal para implantar uma VM de uma imagem em outro locatário do Azure. Para criar uma VM de uma imagem compartilhada entre locatários, você deve usar o CLI do Azure ou o [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Criar uma VM usando CLI do Azure

Conecte a entidade de serviço para o locatário 1 usando a appID, a chave do aplicativo e a ID do locatário 1. Você pode usar `az account show --query "tenantId"` para obter as IDs de locatário, se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Conecte a entidade de serviço para o locatário 2 usando a appID, a chave do aplicativo e a ID do locatário 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie a VM. Substitua as informações no exemplo pelo seu próprio.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas, poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).