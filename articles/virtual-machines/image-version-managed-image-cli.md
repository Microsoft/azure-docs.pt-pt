---
title: Migrar de uma imagem gerida para uma versão de imagem com o Azure CLI
description: Aprenda a migrar de uma imagem gerida para uma versão de imagem numa Galeria de Imagem Partilhada usando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 6f49ece874ea52227e6531193fc53b3bea525702
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796606"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Migrar de uma imagem gerida para uma versão de imagem usando o Azure CLI
Se tiver uma imagem gerida existente que gostaria de migrar para uma Galeria de Imagem Partilhada, pode criar uma imagem partilhada da Galeria de Imagens diretamente a partir da imagem gerida. Depois de ter testado a sua nova imagem, pode eliminar a imagem gerida pela fonte. Também pode migrar de uma imagem gerida para uma Galeria de Imagem Partilhada usando [powerShell](image-version-managed-image-powershell.md).

As imagens numa galeria de imagens têm dois componentes, que vamos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se usa uma Galeria de Imagem Partilhada. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é utilizada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, deve ter uma Galeria de [Imagem Partilhada](shared-images-cli.md)existente. 

Para completar o exemplo neste artigo, deve ter uma imagem gerida existente de um VM generalizado. Para mais informações, consulte [Capture uma imagem gerida](./linux/capture-image.md). Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.



## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Como as imagens geridas são sempre imagens generalizadas, criará uma definição de imagem usando `--os-state generalized` para uma imagem generalizada.

Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)de imagem .

Crie uma definição de imagem na galeria utilizando a [criação de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem é denominada *myImageDefinition*, e é para uma imagem de Linux OS [generalizada.](./linux/shared-image-galleries.md#generalized-and-specialized-images) Para criar uma definição para imagens utilizando um Sistema operativo Windows, utilize. `--os-type Windows` 

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

Criar versões utilizando a [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Terá de passar a identificação da imagem gerida para utilizar como base para criar a versão de imagem. Pode usar a [lista de imagens az](/cli/azure/image?view#az-image-list) para obter as identificações para as suas imagens. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar uma réplica na região *centro-sul dos EUA* e uma réplica na região *leste dos EUA 2* utilizando armazenamento redundante. Ao escolher as regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Passe a identificação da imagem `--managed-image` gerida no parâmetro.


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
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar todas as réplicas da sua `--storage-account-type standard_zrs` versão de imagem no [Armazenamento Redundante da Zona,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando cria a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Crie um VM a partir de uma versão de [imagem generalizada.](vm-generalized-image-version-cli.md)