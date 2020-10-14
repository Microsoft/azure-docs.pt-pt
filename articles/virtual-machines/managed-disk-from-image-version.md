---
title: Criar um disco gerido a partir de uma versão de imagem
description: Crie um disco gerido a partir de uma versão de imagem numa galeria de imagens partilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049878"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Criar um disco gerido a partir de uma versão de imagem

Se precisar, pode fazer um disco gerido a partir de uma versão de imagem armazenada numa Galeria de Imagens Partilhadas.


## <a name="cli"></a>CLI

Desa `source` estarindo a variável para o ID da versão de imagem e, em seguida, use [o disco az criar](/cli/azure/disk.md#az_disk_create) para criar o disco gerido. 


Pode ver uma lista de versões de imagem utilizando [a lista de versão az sig.](/cli/azure/sig/image-version.md#az_sig_image_version_list) Neste exemplo, estamos à procura de todas as versões de imagem que fazem parte da definição de imagem *myImageDefinition* na galeria de imagens *myGallery.*

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


Neste exemplo, criamos um disco gerido chamado *myManagedDisk,* na região *eastus,* num grupo de recursos chamado *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Se o disco for um disco de dados, adicione `--gallery-image-reference-lun` para especificar o LUN.

## <a name="powershell"></a>PowerShell

Pode listar todas as versões de imagem utilizando [o Get-AzResource](/powershell/module/az.resources/get-azresource). 

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

Configurar algumas variáveis para a informação do disco.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Crie uma configuração de disco e, em seguida, o disco, utilizando o ID da versão de imagem de origem. Para o `-GalleryImageReference` , LUN só é necessário se a fonte for um disco de dados.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Criar o disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Passos seguintes

Também pode criar uma versão de imagem a partir de um disco gerido utilizando o [Azure CLI](image-version-managed-image-cli.md) ou [o PowerShell](image-version-managed-image-powershell.md).


