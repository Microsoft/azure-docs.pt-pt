---
title: Crie um conjunto de escala a partir de uma imagem generalizada com Azure CLI
description: Crie um conjunto de escala usando uma imagem generalizada numa Galeria de Imagens Partilhadas utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: de50540345ac6170d229549cad736dafb04e488c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792368"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Crie um conjunto de escala a partir de uma imagem generalizada com Azure CLI

Crie um conjunto de escala a partir de uma versão de imagem generalizada armazenada numa [Galeria de Imagens Partilhadas](../virtual-machines/shared-image-galleries.md) utilizando o Azure CLI. Se pretender criar um conjunto de escala utilizando uma versão de imagem especializada, consulte [Configurar instâncias de escala a partir de uma imagem especializada](instance-specialized-image-version-cli.md).

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.4.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

Substitua os nomes de recursos necessários neste exemplo. 

Listar as definições de imagem numa galeria utilizando [a lista de definições de imagem az sig](/cli/azure/sig/image-definition#az_sig_image_definition_list) para ver o nome e o ID das definições.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Crie o conjunto de escala utilizando [`az vmss create`](/cli/azure/vmss#az_vmss_create) . 

Utilize o ID de definição de imagem `--image` para criar as instâncias definidas na escala a partir da versão mais recente da imagem que está disponível. Também pode criar as instâncias definidas em escala a partir de uma versão específica, fornecendo o ID da versão de imagem para `--image` . Esteja ciente de que usar uma versão de imagem específica significa que a automatização pode falhar se essa versão de imagem específica não estiver disponível porque foi eliminada ou removida da região. Recomendamos a utilização do ID de definição de imagem para criar o seu novo VM, a menos que seja necessária uma versão de imagem específica.

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
[O Azure Image Builder (pré-visualização)](../virtual-machines/image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../virtual-machines/shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../virtual-machines/troubleshooting-shared-images.md)