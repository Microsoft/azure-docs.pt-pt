---
title: Criar um VM a partir de uma versão de imagem especializada usando o Azure CLI
description: Crie um VM utilizando uma versão de imagem especializada numa Galeria de Imagens Partilhadas utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe081b0e74acf771e10406c15a3dea4e09956c37
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560973"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Criar um VM utilizando uma versão de imagem especializada com o Azure CLI

Crie um VM a partir de uma [versão de imagem especializada](./shared-image-galleries.md#generalized-and-specialized-images) armazenada numa Galeria de Imagens Partilhadas. Se quiser criar um VM utilizando uma versão de imagem generalizada, consulte [Criar um VM a partir de uma versão de imagem generalizada](vm-generalized-image-version-cli.md).

Substitua os nomes de recursos necessários neste exemplo. 

Listar as definições de imagem numa galeria utilizando [a lista de definições de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e o ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Crie o VM utilizando [a az vm criar](/cli/azure/vm#az-vm-create) usando o parâmetro --especializado para indicar que a imagem é uma imagem especializada. 

Utilize o ID de definição de imagem `--image` para criar o VM a partir da versão mais recente da imagem que está disponível. Também pode criar o VM a partir de uma versão específica, fornecendo o ID da versão de imagem para `--image` . 

Neste exemplo, estamos a criar um VM a partir da versão mais recente da imagem *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./linux/image-builder-gallery-update-image-version.md) 

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)