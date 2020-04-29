---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241213"
---
## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/powershell](https://shell.azure.com/powershell)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="get-the-managed-image"></a>Obtenha a imagem gerida

Pode ver uma lista de imagens que estão disponíveis num grupo de recursos usando [o Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Assim que souber o nome da imagem e em `Get-AzImage` que grupo de recursos se encontra, pode voltar a usar para obter o objeto de imagem e armazená-lo numa variável para usar mais tarde. Este exemplo obtém uma imagem chamada *myImage* do grupo de recursos "myResourceGroup" e atribui-a à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços. Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens utilizando a [New-AzGallery.](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) O exemplo seguinte cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG.*

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

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)de imagem .

Crie a definição de imagem utilizando a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem da galeria chama-se *myGalleryImage*.

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

Crie uma versão de imagem a partir de uma imagem gerida utilizando [a New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão de imagem é *de 1.0.0* e é replicada tanto para centros de dados do *Centro Central Oeste* dos EUA como para os centros de dados do Centro Central Sul *dos EUA.* Ao escolher as regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.


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

Pode levar algum tempo a replicar a imagem a todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso do `$job.State`trabalho, escreva.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem. 
>
> Também pode armazenar a sua versão de `-StorageAccountType Standard_ZRS` imagem no [Armazenamento Redundante da Zona,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando cria a versão de imagem.
>


## <a name="share-the-gallery"></a>Partilhar a galeria

Recomendamos que partilhe o acesso ao nível da galeria de imagens. Utilize um endereço de e-mail e o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para obter o ID do objeto para o utilizador, em seguida, use [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para dar-lhes acesso à galeria. Substitua o alinne_montes@contoso.com e-mail de exemplo, neste exemplo, com a sua própria informação.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```