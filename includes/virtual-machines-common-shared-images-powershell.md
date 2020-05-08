---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792195"
---
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

