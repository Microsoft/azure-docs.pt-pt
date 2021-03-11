---
title: Criar um VM a partir de uma imagem generalizada usando o Azure CLI
description: Crie um VM a partir de uma versão de imagem generalizada utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551047"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Criar um VM a partir de uma versão de imagem generalizada usando o CLI

Crie um VM a partir de uma [versão de imagem generalizada](./shared-image-galleries.md#generalized-and-specialized-images) armazenada numa Galeria de Imagens Partilhadas. Se pretender criar um VM utilizando uma imagem especializada, consulte [Criar um VM a partir de uma imagem especializada.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>Obtenha a imagem ID

Listar as definições de imagem numa galeria utilizando [a lista de definições de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e o ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Criar a VM

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Para utilizar a versão mais recente da imagem, `--image` desafine-se no ID da definição de imagem. 

Substitua os nomes de recursos necessários neste exemplo. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Também pode utilizar uma versão específica utilizando o ID da versão de imagem para o `--image` parâmetro. Por exemplo, para utilizar a versão de imagem *1.0.0* tipo: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Passos seguintes

[O Azure Image Builder (pré-visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./linux/image-builder-gallery-update-image-version.md)