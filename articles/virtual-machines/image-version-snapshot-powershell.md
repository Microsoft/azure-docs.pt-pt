---
title: PowerShell - Criar uma imagem a partir de um instantâneo ou disco gerido numa Galeria de Imagens Partilhadas
description: Saiba como criar uma imagem a partir de um instantâneo ou disco gerido numa Galeria de Imagens Partilhadas utilizando o PowerShell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556908"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Crie uma imagem a partir de um disco gerido ou instantâneo numa Galeria de Imagens Partilhadas usando PowerShell

Se tiver um instantâneo ou disco gerido existente que gostaria de migrar para uma Galeria de Imagens Partilhadas, pode criar uma imagem da Galeria de Imagens Partilhada diretamente do Disco Gerido ou instantâneo. Depois de ter testado a sua nova imagem, pode eliminar o disco gerido ou o instantâneo de origem. Também pode criar uma imagem a partir de um disco gerido ou instantâneo numa Galeria de Imagens Partilhadas utilizando o [Azure CLI](image-version-snapshot-cli.md).

As imagens numa galeria de imagens têm dois componentes, que iremos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, tem de ter uma foto ou disco gerido. 

Se pretender incluir um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.


## <a name="get-the-snapshot-or-managed-disk"></a>Obtenha o instantâneo ou disco gerido

Pode ver uma lista de instantâneos disponíveis num grupo de recursos utilizando [o Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Assim que souber o nome instantâneo e em que grupo de recursos está, pode `Get-AzSnapshot` usar novamente para obter o objeto instantâneo e armazená-lo numa variável para usar mais tarde. Este exemplo obtém um instantâneo chamado *mySnapshot* do grupo de recursos "myResourceGroup" e atribui-o à variável *$source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Também pode utilizar um Disco Gerido em vez de um instantâneo. Para obter um Disco Gerido, utilize [o Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Em seguida, pegue o Disco Gerido e atribua-o à `$source` variável.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Pode utilizar os mesmos cmdlets para obter quaisquer discos de dados que pretenda incluir na sua imagem. Atribua-as a variáveis e, em seguida, use essas variáveis mais tarde quando criar a versão de imagem.


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

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Neste exemplo, estamos assumindo que o instantâneo ou o Disco Gerido são de um VM que está em uso, e não foi generalizado. Se o Disco Gerido ou instantâneo foi tirado de um SISTEMA generalizado (depois de executar Sysprep para Windows ou [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou para `-deprovision+user` Linux) então mude o `-OsState` para `generalized` . 

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./shared-image-galleries.md#image-definitions).

Crie a definição de imagem utilizando [a Definição de Imagem de Nova AzGallery](/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e destina-se a um Sistema operativo especializado do Windows. Para criar uma definição para imagens usando um Sistema Operativo Linux, utilize `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Informações do plano de compra

Em alguns casos, você precisa passar informações do plano de compra ao criar um VM a partir de uma imagem que foi baseada numa imagem do Azure Marketplace. Nestes casos, recomendamos que inclua a informação do plano de compra na definição de imagem. Neste caso, consulte [informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir do instantâneo utilizando [a Versão Nova-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Se quiser que a sua imagem contenha um disco de dados, para além do disco DE, adicione o `-DataDiskImage` parâmetro e desa quando o coloque no ID do instantâneo do disco de dados ou no Disco Gerido.

Neste exemplo, a versão de imagem é *1.0.0* e é replicada tanto para os centros de dados do *Centro Central Oeste dos EUA* como para os centros de dados do Centro Central Sul dos *EUA.* Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Pode levar algum tempo a replicar a imagem em todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso do trabalho, `$job.State` escreva.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar o mesmo instantâneo para criar outra versão de imagem. 
>
> Também pode armazenar a sua versão de imagem em [Zone Redundant Storage](../storage/common/storage-redundancy.md) adicionando quando criar a versão de `-StorageAccountType Standard_ZRS` imagem.
>

## <a name="delete-the-source"></a>Eliminar a fonte

Uma vez verificada que a nova versão da imagem está a funcionar corretamente, pode eliminar a fonte da imagem com [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) ou [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Passos seguintes

Uma vez verificada a replicação, pode criar um VM a partir da [imagem especializada.](vm-specialized-image-version-powershell.md)

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)