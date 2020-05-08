---
title: Criar um conjunto de escala a partir de uma imagem generalizada
description: Crie um conjunto de escala utilizando uma imagem generalizada numa Galeria de Imagem Partilhada.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 59e29be2aade993c8aeae64b4aa4918b36a26b26
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797139"
---
# <a name="create-a-scale-set-from-a-generalized-image"></a>Criar um conjunto de escala a partir de uma imagem generalizada

Crie um conjunto de escala a partir de uma versão de imagem generalizada armazenada numa Galeria de [Imagem Partilhada](shared-image-galleries.md) utilizando o Azure CLI. Se quiser criar um conjunto de escala utilizando uma versão de imagem especializada, consulte [Criar instâncias](instance-specialized-image-version-cli.md)de conjunto de escala a partir de uma imagem especializada .

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

Crie o [`az vmss create`](/cli/azure/vmss#az-vmss-create)conjunto de calcário utilizando . 

Utilize o ID `--image` de definição de imagem para criar as instâncias de conjunto de escala a partir da versão mais recente da imagem que está disponível. Também pode criar as instâncias de conjunto de escala a `--image`partir de uma versão específica, fornecendo o ID da versão de imagem para . Esteja ciente de que usar uma versão de imagem específica significa que a automatização pode falhar se essa versão de imagem específica não estiver disponível porque foi eliminada ou removida da região. Recomendamos a utilização do ID de definição de imagem para criar o seu novo VM, a menos que seja necessária uma versão de imagem específica.

Neste exemplo, estamos a criar instâncias a partir da versão mais recente da imagem *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](../virtual-machines/linux/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)