---
title: Criar uma imagem a partir de um VM (Pré-visualização)
description: Aprenda a usar o Azure PowerShell para criar uma imagem numa Galeria de Imagem Partilhada a partir de um VM existente em Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 454ed810f950924d3dd790a2442fe29816bf940d
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838472"
---
# <a name="preview-create-an-image-from-a-vm"></a>Pré-visualização: Criar uma imagem a partir de um VM

Se tiver um VM existente que gostaria de usar para fazer MMs múltiplos e idênticos, pode usar esse VM para criar uma imagem numa Galeria de Imagem Partilhada utilizando o Azure PowerShell. Também pode criar uma imagem a partir de um VM utilizando o [Azure CLI](image-version-vm-cli.md).

Pode capturar uma imagem de VMs [especializados e generalizados](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) usando o Azure PowerShell. 

As imagens numa galeria de imagens têm dois componentes, que vamos criar neste exemplo:
- Uma **definição de Imagem** transporta informações sobre a imagem e requisitos para a sua utilização. Isto inclui se a imagem é Windows ou Linux, especializada ou generalizada, notas de lançamento e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. 
- Uma **versão de imagem** é o que é usado para criar um VM quando se usa uma Galeria de Imagem Partilhada. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se cria um VM, a versão de imagem é utilizada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma Galeria de Imagem Partilhada existente, e um VM existente em Azure para usar como fonte. 

Se o VM tiver um disco de dados ligado, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua os nomes de recursos sempre que necessário.


## <a name="get-the-gallery"></a>Pegue a galeria

Pode listar todas as galerias e definições de imagem pelo nome. Os resultados estão no formato `gallery\image definition\image version`.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Assim que encontrar as definições de galeria e imagem certas, crie variáveis para que possam usar mais tarde. Este exemplo recebe a galeria chamada *myGallery* no grupo de recursos *myResourceGroup.*

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Obter o VM

Pode ver uma lista de VMs que estão disponíveis num grupo de recursos usando [o Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Assim que souber o nome VM e em que `Get-AzVM` grupo de recursos se encontra, pode voltar a usar para obter o objeto VM e armazená-lo numa variável para usar mais tarde. Este exemplo obtém um VM chamado *fonteVM* do grupo de recursos "myResourceGroup" e atribui-o à variável *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

É uma boa prática parar\deallocate o VM antes de criar uma imagem usando [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São usados para gerir informação sobre a imagem. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Ao fazer a definição de imagem, certifique-se de que tem todas as informações corretas. Se generalizou o VM (utilizando sysprep para Windows, ou waagent -deprovisionamento para `-OsState generalized`Linux), então deve criar uma definição de imagem utilizando . Se não generalizou o VM, crie `-OsState specialized`uma definição de imagem usando .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)de imagem .

Crie a definição de imagem utilizando a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e destina-se a um VM especializado a executar Windows. Para criar uma definição para `-OsType Linux`imagens que utilizem o Linux, utilize . 

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

Crie uma versão de imagem utilizando a Versão De Imagem [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão de imagem é *de 1.0.0* e é replicada tanto para centros de dados do *Centro Central Oeste* dos EUA como para os centros de dados do Centro Central Sul *dos EUA.* Ao escolher as regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Para criar uma versão de imagem `$vm.Id.ToString()` a `-Source`partir do VM, utilize para o .

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
   -Source $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Pode levar algum tempo a replicar a imagem a todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso do `$job.State`trabalho, escreva.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no `-StorageAccountType Premium_LRS`armazenamento Premiun através `-StorageAccountType Standard_ZRS` de um armazenamento de adição ou [Zona Redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando criar a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Depois de ter verificado que a nova versão da imagem está a funcionar corretamente, pode criar um VM. Crie um VM a partir de uma [versão de imagem especializada](vm-specialized-image-version-powershell.md) ou de uma versão de imagem [generalizada.](vm-generalized-image-version-powershell.md)
