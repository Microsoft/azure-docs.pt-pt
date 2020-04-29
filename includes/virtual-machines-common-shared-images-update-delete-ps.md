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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184222"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações no que pode ser atualizado. Os seguintes itens podem ser atualizados: 

Galeria de imagens partilhadas:
- Descrição

Definição de imagem:
- VCPUs recomendado
- Memória recomendada
- Descrição
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Exclusão dos últimos
- Data de fim de vida

Se pretender adicionar réplicas de regiões, não apague a imagem gerida pela fonte. A imagem gerida pela fonte é necessária para replicar a versão de imagem para regiões adicionais. 

Para atualizar a descrição de uma galeria, utilize [a Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Este exemplo mostra como usar [update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) para atualizar a data de fim de vida para a nossa definição de imagem.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Este exemplo mostra como usar [update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) para excluir que esta versão de imagem seja usada como a *imagem mais recente.*

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Limpar recursos

Ao apagar recursos, precisa começar com o último item nos recursos aninhados - a versão de imagem. Uma vez eliminadas as versões, pode eliminar a definição de imagem. Não pode apagar a galeria até que todos os recursos por baixo dela tenham sido apagados.

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

