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
ms.openlocfilehash: 841027fe8d6b97e661faa038dc9381edbb3d4cd8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226002"
---
## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, tem de ter uma imagem gerida existente de uma VM generalizada. Para obter mais informações, consulte [Tutorial: Criar uma imagem personalizada de uma VM do Azure com a CLI 2.0 do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Se a imagem gerida contém um disco de dados, o tamanho do disco de dados não pode ser mais de 1 TB.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar a CLI localmente, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal utilizado para ativar a partilha de imagens. Carateres permitidos para o nome de galeria são letras em maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome de Galeria não pode conter traços.   Nomes de galeria tem de ser exclusivos na sua subscrição. 

Crie uma galeria de imagens com [az sig criar](/cli/azure/sig#az-sig-create). O exemplo seguinte cria uma galeria com o nome *myGallery* na *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem

Definições de imagem, criar um agrupamento lógico das imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criados dentro dos mesmos. Nomes de definição de imagem podem ser constituídos por letras em maiúsculas ou minúsculas, dígitos, pontos, travessões e períodos. Para obter mais informações sobre os valores que pode especificar para obter uma definição de imagem, veja [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Criar uma definição de imagem inicial na galeria utilizando [criação da definição de imagem do sig az](/cli/azure/sig/image-definition#az-sig-image-definition-create).

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

Criar versões da imagem, conforme necessário através de [az Galeria criar-imagem-versão da imagem](/cli/azure/sig/image-version#az-sig-image-version-create). Terá de introduzir o ID da imagem gerida a utilizar como uma linha de base para criar a versão da imagem. Pode usar [lista de imagens de az](/cli/azure/image?view#az-image-list) para obter informações sobre imagens que estão num grupo de recursos. 

Carateres permitidos para a versão da imagem são números e pontos finais. Tem de ser números dentro do intervalo de um número inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, é a versão da nossa imagem *1.0.0* e, vamos criar 2 réplicas no *e.u.a. Centro-Oeste* região, 1 réplica no *Centro-Sul* região e 1 réplica no *E.U.A. Leste 2* região a utilizar o armazenamento com redundância de zona.


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
> Terá de aguardar que a versão da imagem concluir completamente a ser criada e replicadas antes de poder utilizar a mesma imagem gerida para criar outra versão da imagem.
>
> Pode também armazenar todas as réplicas de versão de imagem na [armazenamento com redundância de zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando `--storage-account-type standard_zrs` ao criar a versão da imagem.
>

## <a name="share-the-gallery"></a>Partilhar a Galeria

Recomendamos que partilha com outros utilizadores ao nível da galeria. Para obter o ID de objeto da galeria, utilize [show de sig az](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilizar o ID de objeto como um âmbito, juntamente com um endereço de e-mail e [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create) para conceder acesso de utilizador para a Galeria de imagem partilhada.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


