---
title: Criar um VM a partir de uma versão de imagem especializada usando o Azure CLI
description: Crie um VM utilizando uma versão de imagem especializada numa Galeria de Imagem Partilhada utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796775"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Crie um VM utilizando uma versão de imagem especializada com o Azure CLI

Crie um VM a partir de uma [versão de imagem especializada](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) armazenada numa Galeria de Imagem Partilhada. Se quiser criar um VM utilizando uma versão de imagem generalizada, consulte [Criar um VM a partir de uma versão](vm-generalized-image-version-cli.md)de imagem generalizada .

Substitua os nomes de recursos conforme necessário neste exemplo. 

Enumera as definições de imagem numa galeria utilizando a [lista de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list) para ver o nome e identificação das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Criar o VM usando [az vm criar](/cli/azure/vm#az-vm-create) usando o parâmetro --especializado para indicar que a imagem é uma imagem especializada. 

Utilize o ID `--image` de definição de imagem para criar o VM a partir da versão mais recente da imagem que está disponível. Também pode criar o VM a partir de uma `--image`versão específica, fornecendo o ID da versão de imagem para . 

Neste exemplo, estamos a criar um VM a partir da versão mais recente da imagem *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](./linux/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](./linux/image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


