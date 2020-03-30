---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814784"
---
## <a name="before-you-begin"></a>Antes de começar

Para completar o exemplo neste artigo, deve ter uma imagem gerida existente de um VM generalizado. Para mais informações, consulte [Tutorial: Crie uma imagem personalizada de um VM Azure com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/bash](https://shell.azure.com/bash)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens usando [az sig criar.](/cli/azure/sig#az-sig-create) O exemplo seguinte cria uma galeria chamada *myGallery* na *myGalleryRG.*

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)de imagem .

Crie uma definição de imagem inicial na galeria utilizando a [criação de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem 

Crie versões da imagem conforme necessário usando a [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Terá de passar a identificação da imagem gerida para utilizar como base para criar a versão de imagem. Pode usar a [lista de imagens az](/cli/azure/image?view#az-image-list) para obter informações sobre imagens que estão num grupo de recursos. 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão da nossa imagem é *de 1.0.0* e vamos criar 2 réplicas na região *centro-oeste dos EUA,* 1 réplica na região *centro-sul dos EUA* e 1 réplica na região leste dos EUA *2* utilizando armazenamento redundante.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar todas as réplicas da sua `--storage-account-type standard_zrs` versão de imagem no [Armazenamento Redundante da Zona,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando cria a versão de imagem.
>

## <a name="share-the-gallery"></a>Partilhar a galeria

Recomendamos que partilhe com outros utilizadores ao nível da galeria. Para obter a identificação do objeto da sua galeria, use [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilize o ID do objeto como âmbito, juntamente com um endereço de e-mail e [uma atribuição de função az criar](/cli/azure/role/assignment#az-role-assignment-create) para dar a um utilizador acesso à galeria de imagens partilhadas.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


