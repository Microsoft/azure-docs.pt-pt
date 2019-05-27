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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145767"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Podem ser atualizados os seguintes itens: 

Galeria de imagem partilhada:
- Descrição

definição de imagem:
- VCPUs recomendada
- Memória recomendada
- Descrição
- Data de vida de fim de

Versão da imagem:
- Contagem de réplicas regional
- Regiões de destino
- Exclusão de versão mais recente
- Data de vida de fim de

Se pretender adicionar regiões de réplica, não elimine a imagem gerida de origem. A imagem gerida de origem é necessária para replicar a versão da imagem a mais regiões. 

Para atualizar a descrição de uma galeria, utilize [AzGallery atualização](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Este exemplo mostra como usar [AzGalleryImageDefinition atualização](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) para atualizar a data de fim de vida para nossa definição de imagem.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Este exemplo mostra como usar [atualização AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) para impedir que esta versão da imagem a ser utilizado como o *mais recente* imagem.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Limpar recursos

Quando a eliminação de recursos, precisa para começar com o último item recursos aninhados - a versão da imagem. Depois de versões são eliminadas, pode eliminar a definição de imagem. Não é possível eliminar a Galeria até que todos os recursos abaixo dele tem sido eliminados.

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

