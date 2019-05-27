---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 82187b05a398c066f9da94c57cbe8a59a6ba3275
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145776"
---
## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="get-the-managed-image"></a>Obter a imagem gerida

Pode ver uma lista de imagens que estão disponíveis num grupo de recursos utilizando [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Depois que souber o nome da imagem e o grupo de quais recursos ele é, pode usar `Get-AzImage` novamente para obter o objeto de imagem e o armazenamos numa variável para utilizar mais tarde. Neste exemplo obtém uma imagem designada *myImage* do grupo de recursos "myResourceGroup" e o atribui à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal utilizado para ativar a partilha de imagens. Carateres permitidos para o nome de galeria são letras em maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome de Galeria não pode conter traços. Nomes de galeria tem de ser exclusivos na sua subscrição. 

Crie uma galeria de imagens com [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). O exemplo seguinte cria uma galeria com o nome *myGallery* no *myGalleryRG* grupo de recursos.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem, criar um agrupamento lógico das imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criados dentro dos mesmos. Nomes de definição de imagem podem ser constituídos por letras em maiúsculas ou minúsculas, dígitos, pontos, travessões e períodos. Para obter mais informações sobre os valores que pode especificar para obter uma definição de imagem, veja [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Criar a definição de imagem com [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem de galeria é denominada *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Criar uma versão de imagem a partir de uma imagem gerida utilizando [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Carateres permitidos para a versão da imagem são números e pontos finais. Tem de ser números dentro do intervalo de um número inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

Neste exemplo, é a versão da imagem *1.0.0* e ele é replicado para ambas *e.u.a. Centro-Oeste* e *Centro-Sul* centros de dados. Ao escolher as regiões de destino para replicação, lembre-se de que também tem de incluir o *origem* região como um destino para replicação.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Pode demorar algum tempo para replicar a imagem para todas as regiões de destino, portanto, criamos uma tarefa para que possa acompanhar o progresso. Para ver o progresso da tarefa, escreva `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Terá de aguardar que a versão da imagem concluir completamente a ser criada e replicadas antes de poder utilizar a mesma imagem gerida para criar outra versão da imagem. 
>
> Pode também armazenar sua versão de imagem na [armazenamento com redundância de zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando `-StorageAccountType Standard_ZRS` ao criar a versão da imagem.
>
