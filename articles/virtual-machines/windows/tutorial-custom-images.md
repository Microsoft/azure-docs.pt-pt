---
title: Tutorial - Crie imagens VM personalizadas com A PowerShell Azure
description: Neste tutorial, aprende-se a usar o Azure PowerShell para criar uma imagem virtual personalizada do Windows armazenada numa Galeria de Imagem Partilhada Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9061cbbae0b30881fffe1762208216cb8009594a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791583"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Tutorial: Criar imagens VM do Windows com A PowerShell Azure

As imagens podem ser usadas para a colocação de botas e garantir a consistência em vários VMs. Neste tutorial, cria a sua própria imagem especializada de uma máquina virtual Azure usando a PowerShell e armazena-a numa Galeria de Imagem Partilhada. Saiba como:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um VM a partir de uma imagem 
> * Partilhar uma galeria de imagens



## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como pegar num VM existente e transformá-lo numa imagem personalizada reutilizável que pode usar para criar novos VMs.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se necessário, pode ver o [powerShell quickstart](quick-create-powershell.md) para criar um VM para usar para este tutorial. Ao trabalhar através do tutorial, substitua os nomes de recursos sempre que necessário.

## <a name="overview"></a>Descrição geral

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. 

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/powershell](https://shell.azure.com/powershell)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="get-the-vm"></a>Obter o VM

Pode ver uma lista de VMs que estão disponíveis num grupo de recursos usando [o Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Assim que souber o nome VM e `Get-AzVM` que grupo de recursos, pode voltar a usar para obter o objeto VM e armazená-lo numa variável para usar mais tarde. Este exemplo obtém um VM chamado *fonteVM* do grupo de recursos "myResourceGroup" e atribui-o à variável *$vm*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. No exemplo seguinte, um grupo de recursos chamado *myGalleryRG* é criado na região *de EastUS:*

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. Os caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços. Os nomes das galerias devem ser únicos dentro da sua subscrição. 

Crie uma galeria de imagens utilizando a [New-AzGallery.](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) O exemplo seguinte cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG.*

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)de imagem .

Crie a definição de imagem utilizando a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem da galeria chama-se *myGalleryImage* e é criada para uma imagem especializada. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir de um VM utilizando [a New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão de imagem é *de 1.0.0* e é replicada tanto para centros de dados dos *EUA Orientais* como *para o Centro Sul dos EUA.* Ao escolher as regiões-alvo para replicação, é necessário incluir a região *de origem* como alvo de replicação.

Para criar uma versão de imagem `$vm.Id.ToString()` a `-Source`partir do VM, utilize para o .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $vm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Pode levar algum tempo a replicar a imagem a todas as regiões-alvo.


## <a name="create-a-vm"></a>Criar uma VM 

Uma vez que tenha uma imagem especializada, pode criar um ou mais Novos VMs. Utilizando o cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Para utilizar a imagem, utilize ''Set-AzVMSourceImage` and set the `-Id' para o ID de definição de imagem ($galleryImage.Id neste caso) para utilizar sempre a versão mais recente da imagem. 

Substitua os nomes de recursos conforme necessário neste exemplo. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
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
   
## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

A Azure também oferece um serviço, construído em Packer, [Azure VM Image Builder.](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) Basta descrever as suas personalizações num modelo, e tratará da criação de imagem. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem vm especializada. Aprendeu a:

> [!div class="checklist"]
> * Criar um Shared Image Gallery
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um VM a partir de uma imagem 
> * Partilhar uma galeria de imagens

Avance para o próximo tutorial para aprender como criar máquinas virtuais altamente disponíveis.

> [!div class="nextstepaction"]
> [Criar VMs de elevada disponibilidade](tutorial-availability-sets.md)



