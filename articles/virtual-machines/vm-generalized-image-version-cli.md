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
ms.openlocfilehash: a5e0e5544c5e66f43b56de49beaa3ef3932d33f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776884"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Criar um VM a partir de uma versão de imagem generalizada usando o Azure CLI

Crie um VM a partir de uma [versão de imagem generalizada](./shared-image-galleries.md#generalized-and-specialized-images) armazenada numa Galeria de Imagens Partilhadas. Se pretender criar um VM utilizando uma imagem especializada, consulte [Criar um VM a partir de uma imagem especializada.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>Obtenha a imagem ID

Listar as definições de imagem numa galeria utilizando [a lista de definições de imagem az sig](/cli/azure/sig/image-definition#az_sig_image_definition_list) para ver o nome e o ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Criar a VM

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). Para utilizar a versão mais recente da imagem, `--image` desafine-se no ID da definição de imagem. 

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