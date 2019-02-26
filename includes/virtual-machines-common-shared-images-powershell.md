---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: c74a96e3dcce1394e0af5447c07ad38c54b960fa
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825474"
---
## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="preview-register-the-feature"></a>Pré-visualização: Registar a funcionalidade

Galerias de imagem partilhada está em pré-visualização, mas tem de registar a funcionalidade antes de poder utilizá-lo. Para registar a funcionalidade de galerias de imagem partilhada:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Obter a imagem gerida

Pode ver uma lista de imagens que estão disponíveis num grupo de recursos utilizando [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Depois que souber o nome da imagem e o grupo de quais recursos ele é, pode usar `Get-AzImage` novamente para obter o objeto de imagem e o armazenamos numa variável para utilizar mais tarde. Neste exemplo obtém uma imagem designada *myImage* do grupo de recursos "myResourceGroup" e o atribui à variável *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal utilizado para ativar a partilha de imagens. Nomes de galeria tem de ser exclusivos na sua subscrição. Crie uma galeria de imagens com [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). O exemplo seguinte cria uma galeria com o nome *myGallery* no *myGalleryRG* grupo de recursos.

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

Criar a imagem de galeria definição com [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem de galeria é denominada *myGalleryImage*.

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
### <a name="using-publisher-offer-and-sku"></a>Utilizar o publicador, oferta, SKU e 
Para clientes que planeiam na implementação de Imagens partilhadas, **numa versão futura**, poderá usar seu pessoal definidos **-publicador**, **-oferecem** e **- Sku** valores para encontrar e especifique uma definição de imagem, em seguida, criar uma VM com a versão mais recente da imagem da correspondência de definição de imagem. Por exemplo, Eis três definições de imagem e os respetivos valores:

|Definição da Imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Testes|standardOffer|testSku|

Todos os três deles têm exclusivos conjuntos de valores. Pode ter versões de imagem que compartilham um ou dois, mas nem todos os três valores. **Numa versão futura**, será possível combinar estes valores para solicitar a versão mais recente de uma imagem específica. **Esta opção não funcionar na versão atual**, mas estará disponível no futuro. Quando lançada, deve ser utilizado com a seguinte sintaxe para definir a imagem de origem como *myImage1* da tabela acima.

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Isto é semelhante à forma como pode atualmente especificar utilização publicador, oferta e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) para obter a versão mais recente de uma imagem do Marketplace. Com isso em mente, cada definição de imagem tem de ter um conjunto exclusivo desses valores.  

##<a name="create-an-image-version"></a>Criar uma versão de imagem

Criar uma versão de imagem a partir de uma imagem gerida utilizando [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) . Neste exemplo, é a versão da imagem *1.0.0* e ele é replicado para ambas *e.u.a. Centro-Oeste* e *Centro-Sul* centros de dados.


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

