---
title: Migrar uma imagem gerida para uma Galeria de Imagem Partilhada
description: Saiba como usar o Azure PowerShell para migrar uma imagem gerida para uma versão de imagem numa Galeria de Imagem Partilhada.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e00538d1112492c5b7f9fc0f91c86df6d3500701
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796593"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Migrar de uma imagem gerida para uma imagem partilhada da Galeria de Imagens

Se tiver uma imagem gerida existente que gostaria de migrar para uma Galeria de Imagem Partilhada, pode criar uma imagem partilhada da Galeria de Imagens diretamente a partir da imagem gerida. Depois de ter testado a sua nova imagem, pode eliminar a imagem gerida pela fonte. Também pode migrar de uma imagem gerida para uma Galeria de Imagem Partilhada utilizando o [Azure CLI](image-version-managed-image-cli.md).

As imagens numa galeria de imagens têm dois componentes, que vamos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se usa uma Galeria de Imagem Partilhada. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é utilizada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, deve ter uma imagem gerida existente. Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.

## <a name="get-the-gallery"></a>Pegue a galeria

Pode listar todas as galerias e definições de imagem pelo nome. Os resultados estão no formato `gallery\image definition\image version`.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Assim que encontrar a galeria certa, crie uma variável para usar mais tarde. Este exemplo recebe a galeria chamada *myGallery* no grupo de recursos *myResourceGroup.*

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São usados para gerir informação sobre a imagem. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Como as imagens geridas são `-OsState generalized`sempre generalizadas, deve definir. 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)de imagem .

Crie a definição de imagem utilizando a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a definição de imagem é denominada *myImageDefinition*, e é para um Sistema Operativo Do Windows generalizado. Para criar uma definição para imagens `-OsType Linux`utilizando um Sistema Operativo Linux, utilize . 

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

Pode ver uma lista de imagens que estão disponíveis num grupo de recursos usando [o Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Assim que souber o nome da imagem e em `Get-AzImage` que grupo de recursos se encontra, pode voltar a usar para obter o objeto de imagem e armazená-lo numa variável para usar mais tarde. Este exemplo obtém uma imagem chamada *myImage* do grupo de recursos "myResourceGroup" e atribui-a à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir da imagem gerida utilizando a [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão de imagem é *de 1.0.0* e é replicada tanto para centros de dados do *Centro Central Oeste* dos EUA como para os centros de dados do Centro Central Sul *dos EUA.* Ao escolher as regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Pode levar algum tempo a replicar a imagem a todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o `$job.State`progresso, escreva.

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem. 
>
> Também pode armazenar a sua imagem no `-StorageAccountType Premium_LRS`armazenamento Premiun através `-StorageAccountType Standard_ZRS` de um armazenamento de adição ou [Zona Redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando criar a versão de imagem.
>

## <a name="delete-the-managed-image"></a>Eliminar a imagem gerida

Depois de ter verificado que a nova versão da imagem está a funcionar corretamente, pode eliminar a imagem gerida.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Uma vez verificado que a replicação está completa, pode criar um VM a partir da [imagem generalizada](vm-generalized-image-version-powershell.md).

