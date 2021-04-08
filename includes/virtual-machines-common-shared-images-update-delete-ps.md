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
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557098"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Os seguintes itens podem ser atualizados: 

Galeria de imagens partilhada:
- Description

Definição de imagem:
- VCPUs recomendados
- Memória recomendada
- Description
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Exclusão das últimas
- Data de fim de vida

Se planeia adicionar regiões réplicas, não elimine a imagem gerida pela fonte. A imagem gerida pela fonte é necessária para replicar a versão de imagem para regiões adicionais. 

Para atualizar a descrição de uma galeria, utilize [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Este exemplo mostra como usar [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) para atualizar a data de fim de vida para a nossa definição de imagem.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Este exemplo mostra como utilizar a [Actualização-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) para excluir que esta versão de imagem seja usada como a *imagem mais recente.*

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Este exemplo mostra como usar [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) para incluir esta versão de imagem em ser considerada para a *imagem mais recente.*

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao eliminar recursos, é necessário começar com o último item nos recursos aninhados - a versão de imagem. Uma vez eliminadas as versões, pode eliminar a definição de imagem. Não pode apagar a galeria até que todos os recursos abaixo dela tenham sido apagados.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```