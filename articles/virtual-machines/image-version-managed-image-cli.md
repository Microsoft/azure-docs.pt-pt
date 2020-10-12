---
title: Migrar de uma imagem gerida para uma versão de imagem com o Azure CLI
description: Aprenda a migrar de uma imagem gerida para uma versão de imagem numa Galeria de Imagens Partilhadas utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8631a411b26f91bc72e23ac7ff9fb2278f61168c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502890"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Migrar de uma imagem gerida para uma versão de imagem usando o Azure CLI
Se tiver uma imagem gerida existente que gostaria de migrar para uma Galeria de Imagens Partilhadas, pode criar uma imagem da Galeria de Imagens Partilhada diretamente a partir da imagem gerida. Depois de ter testado a sua nova imagem, pode eliminar a imagem gerida pela fonte. Também pode migrar de uma imagem gerida para uma Galeria de Imagens Partilhadas utilizando [o PowerShell.](image-version-managed-image-powershell.md)

As imagens numa galeria de imagens têm dois componentes, que iremos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma Galeria de [Imagem Partilhada](shared-images-cli.md)existente. 

Para completar o exemplo neste artigo, você deve ter uma imagem gerida existente de um VM generalizado. Para obter mais informações, consulte [Capturar uma imagem gerida.](./linux/capture-image.md) Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.



## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Como as imagens geridas são sempre imagens generalizadas, criará uma definição de imagem usando `--os-state generalized` para uma imagem generalizada.

Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./linux/shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria utilizando [a az sig definição de imagem criar](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e destina-se a uma imagem [generalizada](./linux/shared-image-galleries.md#generalized-and-specialized-images) do Linux OS. Para criar uma definição para imagens que utilizem um SISTEMA Windows, utilize `--os-type Windows` . 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Criar versões utilizando [a az galeria de imagens criar versão de imagem](/cli/azure/sig/image-version#az-sig-image-version-create). Terá de passar no ID da imagem gerida para usar como base para criar a versão de imagem. Pode utilizar [a lista de imagens az](/cli/azure/image?view#az-image-list) para obter os IDs para as suas imagens. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 1 réplica na região *sul dos EUA* e 1 réplica na região leste dos EUA *2* usando armazenamento redundante de zona. Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Passe o ID da imagem gerida no `--managed-image` parâmetro.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar todas as réplicas da sua versão de imagem no [Armazenamento Redundante da Zona,](../storage/common/storage-redundancy.md) adicionando `--storage-account-type standard_zrs` quando cria a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Criar um VM a partir de uma [versão de imagem generalizada](vm-generalized-image-version-cli.md).

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
