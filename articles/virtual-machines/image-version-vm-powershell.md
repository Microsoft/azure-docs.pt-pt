---
title: Criar uma imagem a partir de uma VM
description: Aprenda a usar a Azure PowerShell para criar uma imagem numa Galeria de Imagens Partilhadas a partir de um VM existente em Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: a7b8cb10f75d7a99198ddfdc1a1bbef3c34a03da
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685111"
---
# <a name="create-an-image-from-a-vm"></a>Criar uma imagem a partir de uma VM

Se tiver um VM existente que gostaria de usar para fazer VMs múltiplos e idênticos, pode usar esse VM para criar uma imagem numa Galeria de Imagens Partilhada utilizando a Azure PowerShell. Também pode criar uma imagem a partir de um VM utilizando o [Azure CLI](image-version-vm-cli.md).

Pode capturar uma imagem de [VMs especializados e generalizados](./shared-image-galleries.md#generalized-and-specialized-images) utilizando a Azure PowerShell. 

As imagens numa galeria de imagens têm dois componentes, que iremos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, você deve ter uma Galeria de Imagem Partilhada existente, e um VM existente em Azure para usar como fonte. 

Se o VM tiver um disco de dados anexado, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.


## <a name="get-the-gallery"></a>Pegue a galeria

Pode listar todas as galerias e definições de imagem pelo nome. Os resultados estão no `gallery\image definition\image version` formato.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Assim que encontrar a galeria certa e definições de imagem, crie variáveis para que usem mais tarde. Este exemplo obtém a galeria chamada *myGallery* no grupo de recursos *myResourceGroup.*

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Pegue o VM

Pode ver uma lista de VMs que estão disponíveis num grupo de recursos usando [o Get-AzVM](/powershell/module/az.compute/get-azvm). Assim que souber o nome VM e em que grupo de recursos está, pode `Get-AzVM` usar novamente para obter o objeto VM e armazená-lo numa variável para usar mais tarde. Este exemplo obtém um VM nomeado *fonteVM* do grupo de recursos "myResourceGroup" e atribui-o à variável *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

É uma boa prática parar o VM antes de criar uma imagem utilizando [o Stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São usados para gerir informação sobre a imagem. Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Se generalizou o VM (utilizando o Sysprep para Windows, ou desprovisionamento waagent para o Linux), então deve criar uma definição de imagem utilizando `-OsState generalized` . Se não generalizou o VM, crie uma definição de imagem utilizando `-OsState specialized` .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./shared-image-galleries.md#image-definitions).

Crie a definição de imagem utilizando [a Definição de Imagem de Nova AzGallery](/powershell/module/az.compute/new-azgalleryimageversion). 

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e destina-se a um VM especializado que executa o Windows. Para criar uma definição para imagens que utilizem o Linux, `-OsType Linux` utilize. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem utilizando [a Versão Imagens new-AzGallery .](/powershell/module/az.compute/new-azgalleryimageversion) 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão de imagem é *1.0.0* e é replicada tanto para os centros de dados do *Centro Central Oeste dos EUA* como para os centros de dados do Centro Central Sul dos *EUA.* Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Para criar uma versão de imagem a partir do VM, use `$vm.Id.ToString()` para `-SourceImageId` o .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Pode levar algum tempo a replicar a imagem em todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso do trabalho, `$job.State` escreva.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento Premium `-StorageAccountType Premium_LRS` adicionando, ou Zone Redundant [Storage,](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` quando cria a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Uma vez verificada que a nova versão da imagem está a funcionar corretamente, pode criar um VM. Crie um VM a partir de uma [versão de imagem especializada](vm-specialized-image-version-powershell.md) ou de uma versão de imagem [generalizada](vm-generalized-image-version-powershell.md).

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)