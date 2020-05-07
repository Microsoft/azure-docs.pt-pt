---
title: Criar um conjunto de escala a partir de uma versão de imagem especializada usando o Azure CLI
description: Crie um conjunto de escala utilizando uma versão de imagem especializada numa Galeria de Imagem Partilhada utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 5de9fe7c81059c56c99a55ca066e186cbf83c50f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796983"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Criar um conjunto de escala utilizando uma versão de imagem especializada com o Azure CLI

Crie um conjunto de escala a partir de uma [versão de imagem especializada](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) armazenada numa Galeria de Imagem Partilhada. Se quiser criar um conjunto de escala utilizando uma versão de imagem generalizada, consulte [Criar um VM a partir de uma versão](instance-generalized-image-version-cli.md)de imagem generalizada .

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.4.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

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

Criar um conjunto [`az vmss create`](/cli/azure/vmss#az-vmss-create) de `--specialized` escala utilizando o parâmetro para indicar que a imagem é uma imagem especializada.

Utilize o ID `--image` de definição de imagem para criar as instâncias de conjunto de escala a partir da versão mais recente da imagem que está disponível. Também pode criar as instâncias de conjunto de escala a `--image`partir de uma versão específica, fornecendo o ID da versão de imagem para . Esteja ciente de que usar uma versão de imagem específica significa que a automatização pode falhar se essa versão de imagem específica não estiver disponível porque foi eliminada ou removida da região. Recomendamos a utilização do ID de definição de imagem para criar o seu novo VM, a menos que seja necessária uma versão de imagem específica.

Neste exemplo, estamos a criar instâncias a partir da versão mais recente da imagem *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](../virtual-machines/linux/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)



