---
title: Criar um disco gerido a partir de uma versão de imagem
description: Crie um disco gerido a partir de uma versão de imagem numa galeria de imagens partilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 5873f28fed492f9ef906a9d7c1364d8ae07033a7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336066"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Criar um disco gerido a partir de uma versão de imagem

Se necessário, pode exportar o SISTEMA ou um único disco de dados a partir de uma versão de imagem como um disco gerido a partir de uma versão de imagem armazenada numa Galeria de Imagens Partilhada.


## <a name="cli"></a>CLI

Listar as versões de imagem numa galeria utilizando [a lista de versão az sig.](/cli/azure/sig/image-version.md#az_sig_image_version_list) Neste exemplo, estamos à procura de todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition* na galeria de imagens *myGallery.*

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Desa `source` estarindo a variável para o ID da versão de imagem e, em seguida, use [o disco az criar](/cli/azure/disk.md#az_disk_create) para criar o disco gerido. 

Neste exemplo, exportamos o disco OS da versão de imagem para criar um disco gerido chamado *myManagedOSDisk,* na região *eastus,* num grupo de recursos chamado *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Se pretender exportar um disco de dados a partir da versão de imagem, adicione `--gallery-image-reference-lun` para especificar a localização LUN do disco de dados para exportar. 

Neste exemplo, exportamos o disco de dados localizado na LUN 0 da versão de imagem para criar um disco gerido chamado *myManagedDataDisk,* na região *eastus,* num grupo de recursos chamado *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Listar as versões de imagem numa galeria utilizando [o Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Assim que tiver toda a informação de que precisa, pode utilizar [a Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) para obter a versão de imagem de origem que pretende utilizar e atribuí-la a uma variável. Neste exemplo, estamos a obter a `1.0.0` versão de imagem, da `myImageDefinition` definição, na `myGallery` galeria de origem, no `myResourceGroup` grupo de recursos.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Depois de definir a `source` variável para o ID da versão de imagem, use [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) para criar uma configuração de disco e [New-AzDisk](/powershell/module/az.compute/new-azdisk) para criar o disco. 

Neste exemplo, exportamos o disco OS da versão de imagem para criar um disco gerido chamado *myManagedOSDisk,* na região *eastus,* num grupo de recursos chamado *myResourceGroup*. 

Crie uma configuração de disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Criar o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Se pretender exportar um disco de dados na versão de imagem, adicione um ID LUN à configuração do disco para especificar a localização LUN do disco de dados para exportar. 

Neste exemplo, exportamos o disco de dados localizado na LUN 0 da versão de imagem para criar um disco gerido chamado *myManagedDataDisk,* na região *eastus,* num grupo de recursos chamado *myResourceGroup*. 

Crie uma configuração de disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Criar o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Passos seguintes

Também pode criar uma versão de imagem a partir de um disco gerido utilizando o [Azure CLI](image-version-managed-image-cli.md) ou [o PowerShell](image-version-managed-image-powershell.md).


