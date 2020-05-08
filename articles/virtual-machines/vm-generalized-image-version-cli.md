---
title: Criar um VM a partir de uma imagem generalizada usando o Azure CLI
description: Crie um VM a partir de uma versão de imagem generalizada utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.openlocfilehash: 5e59872a4da0136232652008a2980601428eeab6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796788"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Criar um VM a partir de uma versão de imagem generalizada usando o CLI

Crie um VM a partir de uma versão de [imagem generalizada](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) armazenada numa Galeria de Imagem Partilhada. Se quiser criar um VM utilizando uma imagem especializada, consulte [Criar um VM a partir de uma imagem especializada](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Obtenha o ID de imagem

Enumera as definições de imagem numa galeria utilizando a [lista de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e identificação das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Crie a VM

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Para utilizar a versão mais `--image` recente da imagem, desloque-se para a identificação da definição de imagem. 

Substitua os nomes de recursos conforme necessário neste exemplo. 

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

Também pode utilizar uma versão específica utilizando o `--image` ID da versão de imagem para o parâmetro. Por exemplo, utilizar a versão de imagem `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` *1.0.0* tipo: .

## <a name="next-steps"></a>Passos seguintes

[O Azure Image Builder (pré-visualização)](./linux/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](./linux/image-builder-gallery-update-image-version.md) 
