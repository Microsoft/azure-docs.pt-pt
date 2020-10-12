---
title: Copie uma versão de imagem de outra galeria usando o CLI
description: Copie uma versão de imagem de outra galeria com o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e8aabcd1c68272a78b3c1fe88913c5a62496f681
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225840"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Copie uma imagem de outra galeria usando o Azure CLI

Se tiver várias galerias na sua organização, também pode criar versões de imagem a partir de versões de imagem existentes armazenadas noutras galerias. Por exemplo, você pode ter uma galeria de desenvolvimento e teste para criar e testar novas imagens. Quando estiverem prontos para serem utilizados na produção, pode copiá-los numa galeria de produção utilizando este exemplo. Também pode criar uma imagem a partir de uma imagem em outra galeria usando [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma galeria de origem existente, definição de imagem e versão de imagem. Você também deve ter uma galeria de destino. 

A versão de imagem de origem deve ser replicada na região onde está localizada a sua galeria de destino. 

Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.



## <a name="get-information-from-the-source-gallery"></a>Obtenha informações da galeria de origem

Você precisará de informações da definição de imagem de origem para que possa criar uma cópia na sua nova galeria.

Liste informações sobre as galerias de imagem disponíveis usando [a lista az sig](/cli/azure/sig#az-sig-list) para encontrar informações sobre a galeria de origem.

```azurecli-interactive 
az sig list -o table
```

Listar as definições de imagem numa galeria, utilizando [a lista de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-list). Neste exemplo, procuramos definições de imagem na galeria chamada *myGallery* no grupo de recursos *myGalleryRG.*

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Enuste as versões de uma imagem numa galeria, utilizando [a lista de versão az sig](/cli/azure/sig/image-version#az-sig-image-version-list) para encontrar a versão de imagem que pretende copiar na sua nova galeria. Neste exemplo, estamos à procura de todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition.*

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Uma vez que tenha toda a informação de que precisa, pode obter o ID da versão de imagem de origem usando [o az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Criar a definição de imagem 

É necessário criar uma definição de imagem que corresponda à definição de imagem da sua versão de imagem de origem. Pode ver todas as informações necessárias para recriar a definição de imagem na sua nova galeria utilizando [um show de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

A saída será semelhante à seguinte:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Crie uma nova definição de imagem, na sua nova galeria, utilizando as informações da saída acima.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Criar versões utilizando [a az galeria de imagens criar versão de imagem](/cli/azure/sig/image-version#az-sig-image-version-create). Terá de passar no ID da imagem gerida para usar como base para criar a versão de imagem. Pode utilizar [a lista de imagens az](/cli/azure/image?view#az-image-list) para obter informações sobre imagens que estão num grupo de recursos. 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 1 réplica na região *sul dos EUA* e 1 réplica na região leste dos *EUA* usando armazenamento redundante de zona.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento Premium `--storage-account-type  premium_lrs` adicionando, ou Zone Redundant [Storage,](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` quando cria a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Crie um VM a partir de uma versão de imagem [generalizada](vm-generalized-image-version-cli.md) ou [especializada.](vm-specialized-image-version-cli.md)

Além disso, experimente [o Azure Image Builder (pré-visualização)](./linux/image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./linux/image-builder-gallery-update-image-version.md) 

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
