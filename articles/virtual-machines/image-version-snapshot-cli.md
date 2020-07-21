---
title: CLI - Criar uma imagem a partir de um instantâneo ou VHD numa Galeria de Imagens Partilhadas
description: Saiba como criar uma imagem a partir de um instantâneo ou VHD numa Galeria de Imagens Partilhadas utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b5dcadd2381596509a3d2f512d0f4ebbbfbba893
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502882"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Crie uma imagem a partir de um VHD ou instantâneo numa Galeria de Imagens Partilhadas usando o CLI Azure

Se tiver um instantâneo ou VHD existente que gostaria de migrar para uma Galeria de Imagens Partilhadas, pode criar uma imagem da Galeria de Imagens Partilhada diretamente a partir do VHD ou instantâneo. Depois de ter testado a sua nova imagem, pode eliminar a fonte VHD ou a snapshot. Também pode criar uma imagem a partir de um VHD ou instantâneo numa Galeria de Imagens Partilhadas utilizando o [Azure PowerShell](image-version-snapshot-powershell.md).

As imagens numa galeria de imagens têm dois componentes, que iremos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, você deve ter uma foto ou VHD. 

Se pretender incluir um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.

## <a name="find-the-snapshot-or-vhd"></a>Encontre o instantâneo ou VHD 

Pode ver uma lista de instantâneos que estão disponíveis num grupo de recursos utilizando [a lista de instantâneos az](/cli/azure/snapshot#az-snapshot-list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Também pode utilizar um VHD em vez de uma foto instantânea. Para obter um VHD, utilize [a lista de discos az](/cli/azure/disk#az-disk-list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Assim que tiver o ID do instantâneo ou VHD e atribuí-lo a uma variável chamada `$source` para ser usada mais tarde.

Pode utilizar o mesmo processo para obter quaisquer discos de dados que pretenda incluir na sua imagem. Atribua-as a variáveis e, em seguida, use essas variáveis mais tarde quando criar a versão de imagem.


## <a name="find-the-gallery"></a>Encontre a galeria

Necessitará de informações sobre a galeria de imagens para criar a definição de imagem.

Listar informações sobre as galerias de imagem disponíveis usando [a lista az sig](/cli/azure/sig#az-sig-list). Note o nome da galeria que o grupo de recursos que a galeria está a utilizar mais tarde.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São usados para gerir informação sobre a imagem. Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Neste exemplo, estamos assumindo que o instantâneo ou VHD são de um VM que está em uso, e não foi generalizado. Se o VHD ou o instantâneo tiver sido tirado de um SISTEMA generalizado (depois de executar sysprep para Windows ou [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou para `-deprovision+user` Linux) então mude o `-OsState` para `generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./linux/shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria utilizando [a az sig definição de imagem criar](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e [destina-se a](./linux/shared-image-galleries.md#generalized-and-specialized-images) uma imagem especializada do Linux OS. Para criar uma definição para imagens que utilizem um SISTEMA Windows, utilize `--os-type Windows` . 

Neste exemplo, a galeria chama-se *myGallery*, está no grupo de recursos *myGalleryRG,* e o nome de definição de imagem será *mImageDefinition*.

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
   --os-state specialized
```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem utilizando [a az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 1 réplica na região *sul dos EUA* e 1 réplica na região leste dos EUA *2* usando armazenamento redundante de zona. Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* do VHD ou o instantâneo como alvo de replicação.

Passe a identificação do instantâneo ou VHD no `--os-snapshot` parâmetro.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Se pretender incluir discos de dados na imagem, então tem de incluir tanto o conjunto de `--data-snapshot-luns` parâmetros para o número LUN como o `--data-snapshots` conjunto para o ID do disco de dados ou instantâneo.

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar todas as réplicas da sua versão de imagem no [Armazenamento Redundante da Zona,](../storage/common/storage-redundancy.md) adicionando `--storage-account-type standard_zrs` quando cria a versão de imagem.
>

## <a name="next-steps"></a>Próximos passos

Crie um VM a partir de uma [versão de imagem especializada.](vm-specialized-image-version-cli.md)

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
