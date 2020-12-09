---
title: Localizar e utilizar imagens e planos do Azure Marketplace
description: Use a Azure PowerShell para encontrar e utilizar o editor, oferta, SKU, versão e informação de plano para imagens marketplace VM.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906272"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Localizar e utilizar imagens VM do Azure Marketplace com Azure PowerShell     

Este artigo descreve como usar a Azure PowerShell para encontrar imagens VM no Mercado Azure. Em seguida, pode especificar uma imagem do Marketplace e planear informações quando criar um VM.

Também pode navegar nas imagens e ofertas disponíveis utilizando a montra do [Azure Marketplace,](https://azuremarketplace.microsoft.com/) o [portal Azure,](https://portal.azure.com)ou o [Azure CLI.](../linux/cli-ps-findimage.md) 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Criar um VM a partir de VHD com informações de plano

Se tiver um VHD existente que foi criado usando uma imagem do Azure Marketplace, poderá ter de fornecer informações sobre o plano de compra quando criar um novo VM a partir desse VHD.

Se ainda tiver o VM original, ou outro VM criado a partir da mesma imagem, pode obter o nome do plano, editor e informações do produto usando o Get-AzVM. Este exemplo obtém um VM nomeado *myVM* no grupo de recursos *myResourceGroup* e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Se não tiver a informação do plano antes da vm original ser eliminada, pode apresentar um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support). Precisarão do nome VM, id de assinatura e do carimbo de tempo da operação de eliminação.

Para criar um VM utilizando um VHD, consulte este artigo [Crie um VM a partir de um VHD especializado](create-vm-specialized.md) e adicione uma linha para adicionar as informações do plano à configuração VM utilizando o [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) semelhante ao seguinte:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Criar um novo VM a partir de uma imagem de mercado

Se já tiver a informação sobre a imagem que pretende utilizar, pode passar essa informação para [o cmdlet Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) para adicionar informações de imagem à configuração VM. Veja as próximas secções para pesquisar e listar as imagens disponíveis no mercado.

Algumas imagens pagas também exigem que forneça informações do plano de compra utilizando o [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Em seguida, passará a configuração VM juntamente com os outros objetos de configuração para o `New-AzVM` cmdlet. Para obter um exemplo detalhado da utilização de uma configuração VM com PowerShell, consulte este [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Se receber uma mensagem sobre a aceitação dos termos da imagem, consulte a secção [Aceite os termos](#accept-the-terms) mais tarde neste artigo.

## <a name="list-images"></a>Listar imagens

Uma maneira de encontrar uma imagem num local é executar os cmdlets [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)e [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) cmdlets em ordem:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em seguida, para um SKU selecionado, corra [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) para listar as versões a implementar.

1. Listar os editores:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Preencha o nome do seu editor escolhido e enuseie as ofertas:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Preencha o nome da oferta escolhida e enuseie os SKUs:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Preencha o nome SKU escolhido e obtenha a versão de imagem:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A partir da saída do `Get-AzVMImage` comando, pode selecionar uma imagem de versão para implementar uma nova máquina virtual.

O exemplo a seguir mostra a sequência completa de comandos e suas saídas:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Saída parcial:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Para a editora *MicrosoftWindowsServer:*

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Resultado:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Para a oferta *do WindowsServer:*

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Saída parcial:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Em seguida, para o SKU *2019-Datacenter:*

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Agora pode combinar o editor selecionado, oferta, SKU e versão numa URN (valores separados por :). Passe esta URN com o `--image` parâmetro quando criar um VM com o cmdlet [New-AzVM.](/powershell/module/az.compute/new-azvm) Pode substituir opcionalmente o número de versão na URN por "mais recente" para obter a versão mais recente da imagem.

Se implementar um VM com um modelo de Gestor de Recursos, definirá os parâmetros de imagem individualmente nas `imageReference` propriedades. Veja a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Ver propriedades do plano

Para ver as informações do plano de compra de uma imagem, execute o `Get-AzVMImage` cmdlet. Se a `PurchasePlan` propriedade na saída não `null` estiver, a imagem tem termos que deve aceitar antes da implementação programática.  

Por exemplo, a imagem do *Datacenter 2016* do Windows Server não tem termos adicionais, pelo que a `PurchasePlan` informação `null` é:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Resultado:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

O exemplo abaixo mostra um comando semelhante para a Máquina Virtual de Ciência de Dados - imagem *do Windows 2016,* que tem as `PurchasePlan` seguintes propriedades: `name` , e `product` `publisher` . Algumas imagens também têm uma `promotion code` propriedade. Para implementar esta imagem, consulte as seguintes secções para aceitar os termos e para permitir a implementação programática.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Resultado:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Aceitar os termos

Para ver os termos da licença, use o [cmdlet Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passe nos parâmetros do plano de compra. A saída fornece um link para os termos para a imagem marketplace e mostra se você aceitou previamente os termos. Certifique-se de que utiliza todas as letras minúsculas nos valores dos parâmetros.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Resultado:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Utilize o [cmdlet Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) para aceitar ou rejeitar os termos. Só precisa de aceitar termos uma vez por subscrição para a imagem. Certifique-se de que utiliza todas as letras minúsculas nos valores dos parâmetros. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Resultado:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Passos seguintes

Para criar uma máquina virtual rapidamente com o `New-AzVM` cmdlet utilizando informações básicas de imagem, consulte [Criar uma máquina virtual Windows com PowerShell](quick-create-powershell.md).

Para obter mais informações sobre a utilização de imagens do Azure Marketplace para criar imagens personalizadas numa galeria de imagens partilhada, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](../marketplace-images.md)
