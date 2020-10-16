---
title: Migrar uma imagem gerida para uma Galeria de Imagens Partilhadas
description: Aprenda a usar a Azure PowerShell para migrar uma imagem gerida para uma versão de imagem numa Galeria de Imagens Partilhadas.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c1b40cc8d52ffe5655401f7698790cdc05898331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225550"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Migrar de uma imagem gerida para uma imagem da Galeria de Imagens Partilhada

Se tiver uma imagem gerida existente que gostaria de migrar para uma Galeria de Imagens Partilhadas, pode criar uma imagem da Galeria de Imagens Partilhada diretamente a partir da imagem gerida. Depois de ter testado a sua nova imagem, pode eliminar a imagem gerida pela fonte. Também pode migrar de uma imagem gerida para uma Galeria de Imagens Partilhadas utilizando o [Azure CLI](image-version-managed-image-cli.md).

As imagens numa galeria de imagens têm dois componentes, que iremos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma imagem gerida existente. Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.

## <a name="get-the-gallery"></a>Pegue a galeria

Pode listar todas as galerias e definições de imagem pelo nome. Os resultados estão no `gallery\image definition\image version` formato.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Assim que encontrar a galeria certa, crie uma variável para usar mais tarde. Este exemplo obtém a galeria chamada *myGallery* no grupo de recursos *myResourceGroup.*

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São usados para gerir informação sobre a imagem. Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Como as imagens geridas são sempre generalizadas, deve definir `-OsState generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./windows/shared-image-galleries.md#image-definitions).

Crie a definição de imagem utilizando [a Definição de Imagem de Nova AzGallery](/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e destina-se a um Sistema operativo do Windows generalizado. Para criar uma definição para imagens usando um Sistema Operativo Linux, utilize `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Obtenha a imagem gerida

Pode ver uma lista de imagens que estão disponíveis num grupo de recursos utilizando [a Get-AzImage](/powershell/module/az.compute/get-azimage). Assim que souber o nome da imagem e em que grupo de recursos está, pode `Get-AzImage` usar novamente para obter o objeto de imagem e armazená-lo numa variável para usar mais tarde. Este exemplo obtém uma imagem chamada *myImage* do grupo de recursos "myResourceGroup" e atribui-a à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir da imagem gerida utilizando [a Versão Nova-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão de imagem é *1.0.0* e é replicada tanto para os centros de dados do *Centro Central Oeste dos EUA* como para os centros de dados do Centro Central Sul dos *EUA.* Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Pode levar algum tempo a replicar a imagem em todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso, `$job.State` escreva.

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem. 
>
> Também pode armazenar a sua imagem no armazenamento Premium `-StorageAccountType Premium_LRS` adicionando, ou Zone Redundant [Storage,](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` quando cria a versão de imagem.
>

## <a name="delete-the-managed-image"></a>Eliminar a imagem gerida

Uma vez verificada que a nova versão da imagem está a funcionar corretamente, pode eliminar a imagem gerida.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Uma vez verificada a replicação, pode criar um VM a partir da [imagem generalizada](vm-generalized-image-version-powershell.md).

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
