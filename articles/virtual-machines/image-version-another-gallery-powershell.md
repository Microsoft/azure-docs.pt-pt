---
title: Copie uma imagem de outra galeria usando PowerShell
description: Copie uma imagem de outra galeria utilizando a Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2f26716a4d04b574bf393f502758a725948419da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299954"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Copie uma imagem de outra galeria usando PowerShell

Se tiver várias galerias na sua organização, pode criar imagens a partir de imagens armazenadas noutras galerias. Por exemplo, você pode ter uma galeria de desenvolvimento e teste para criar e testar novas imagens. Quando estiverem prontos para serem utilizados na produção, pode copiá-los numa galeria de produção utilizando este exemplo. Também pode criar uma imagem a partir de uma imagem em outra galeria usando o [Azure CLI](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma galeria de origem existente, definição de imagem e versão de imagem. Você também deve ter uma galeria de destino. 

A versão de imagem de origem deve ser replicada na região onde está localizada a sua galeria de destino. 

Vamos criar uma nova definição de imagem e versão de imagem na sua galeria de destino.


Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.


## <a name="get-the-source-image"></a>Obtenha a imagem de origem 

Você precisará de informações da definição de imagem de origem para que possa criar uma cópia na sua galeria de destino.

Liste informações sobre as galerias existentes, definições de imagem e versões de imagem utilizando o cmdlet [Get-AzResource.](/powershell/module/az.resources/get-azresource)

Os resultados estão no `gallery\image definition\image version` formato.

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Uma vez que tenha todas as informações necessárias, pode obter o ID da versão de imagem de origem utilizando [a Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). Neste exemplo, estamos a obter a `1.0.0` versão de imagem, da `myImageDefinition` definição, na `myGallery` galeria de origem, no `myResourceGroup` grupo de recursos.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Criar a definição de imagem 

É necessário criar uma nova definição de imagem que corresponda à definição de imagem da sua fonte. Pode ver todas as informações necessárias para recriar a definição de imagem utilizando [a Definição get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


A saída será semelhante à seguinte:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Crie uma nova definição de imagem, na sua galeria de destino, utilizando o cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) e as informações da saída acima.


Neste exemplo, a definição de imagem é nomeada *myDestinationImgDef* na galeria chamada *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem utilizando [a Versão Imagens new-AzGallery .](/powershell/module/az.compute/new-azgalleryimageversion) Terá de passar no ID da imagem de origem no `--managed-image` parâmetro para criar a versão de imagem na sua galeria de destino. 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a galeria de destino é nomeada *myDestinationGallery,* no grupo de recursos *myDestinationRG,* na localização *dos EUA Ocidentais.* A versão da nossa imagem é *1.0.0* e vamos criar 1 réplica na região *sul dos EUA,* e 2 réplicas na região *oeste dos EUA.* 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Pode levar algum tempo a replicar a imagem em todas as regiões-alvo, por isso criámos um trabalho para podermos acompanhar o progresso. Para ver o progresso do trabalho, `$job.State` escreva.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento premiun através de um armazenamento `-StorageAccountType Premium_LRS` , ou [Armazenamento Redundante zona,](../storage/common/storage-redundancy.md) adicionando `-StorageAccountType Standard_ZRS` quando cria a versão de imagem.
>


## <a name="next-steps"></a>Passos seguintes

Crie um VM a partir de uma versão de imagem [generalizada](vm-generalized-image-version-powershell.md) ou [especializada.](vm-specialized-image-version-powershell.md)

[O Azure Image Builder (pré-visualização)](./linux/image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./linux/image-builder-gallery-update-image-version.md) 

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
