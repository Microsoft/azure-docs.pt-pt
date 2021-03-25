---
title: Localizar e utilizar informações do plano de compra do mercado utilizando o PowerShell
description: Use a Azure PowerShell para encontrar URNs de imagem e parâmetros de plano de compra, como a editora, oferta, SKU, e versão, para imagens Marketplace VM.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 34fd6720b93a1462836b51856d73573a86809367
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022828"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Localizar e utilizar imagens VM do Azure Marketplace com Azure PowerShell

Este artigo descreve como usar a Azure PowerShell para encontrar imagens VM no Mercado Azure. Em seguida, pode especificar uma imagem do Marketplace e planear informações quando criar um VM.

Também pode navegar nas imagens disponíveis e nas ofertas utilizando o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou o [Azure CLI](../linux/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Uma imagem de Marketplace em Azure tem os seguintes atributos:

* **Editora**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadas por uma editora. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Um caso de oferta, como uma grande libertação de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número de versão de uma imagem SKU. 

Estes valores podem ser passados individualmente ou como *URN* de imagem, combinando os valores separados pelo cólon (:). Por exemplo: *Editor*:*Oferta*:*Sku*:*Versão*. Pode substituir o número de versão na URN `latest` para utilizar a versão mais recente da imagem. 

Se o editor de imagem fornecer licença e termos de compra adicionais, então deve aceitá-los antes de poder usar a imagem.  Para mais informações, consulte [Aceitar os termos do plano de compra.](#accept-purchase-plan-terms)

## <a name="list-images"></a>Listar imagens

Pode utilizar o PowerShell para reduzir uma lista de imagens. Substitua os valores das variáveis para satisfazer as suas necessidades.

1. Listar os editores de imagem utilizando [o Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Para um determinado editor, enuseça as suas ofertas utilizando [o Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer).
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Para um determinado editor e oferta, liste os SKUs disponíveis usando [o Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku).
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. Para um SKU, liste as versões da imagem utilizando a [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage).

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Também pode usar `latest` se quiser utilizar a imagem mais recente e não uma versão específica mais antiga.


Agora pode combinar o editor selecionado, oferta, SKU e versão numa URN (valores separados por :). Passe esta URN com o `--image` parâmetro quando criar um VM com o cmdlet [New-AzVM.](/powershell/module/az.compute/new-azvm) Também pode substituir o número de versão na URN `latest` para obter a versão mais recente da imagem.

Se implementar um VM com um modelo de Gestor de Recursos, definirá os parâmetros de imagem individualmente nas `imageReference` propriedades. Veja a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Ver propriedades do plano de compra

Algumas imagens VM no Azure Marketplace têm licença e termos de compra adicionais que deve aceitar antes de poder implantá-las programáticamente. Terá de aceitar os termos da imagem uma vez por subscrição.

Para ver as informações do plano de compra de uma imagem, execute o `Get-AzVMImage` cmdlet. Se a `PurchasePlan` propriedade na saída não `null` estiver, a imagem tem termos que deve aceitar antes da implementação programática.  

Por exemplo, a imagem do *Datacenter 2016* do Windows Server não tem termos adicionais, pelo que a `PurchasePlan` informação `null` é:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

A saída será semelhante à seguinte:

```output
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

A saída será semelhante à seguinte:

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

Para ver os termos da licença, use o [cmdlet Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passe nos parâmetros do plano de compra. A saída fornece um link para os termos para a imagem marketplace e mostra se você aceitou previamente os termos. Certifique-se de que utiliza todas as letras minúsculas nos valores dos parâmetros.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

A saída será semelhante à seguinte:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Aceitar termos do plano de compra

Utilize o [cmdlet Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) para aceitar ou rejeitar os termos. Só precisa de aceitar termos uma vez por subscrição para a imagem. Certifique-se de que utiliza todas as letras minúsculas nos valores dos parâmetros. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
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

Se receber uma mensagem sobre a aceitação dos termos da imagem, consulte a secção anterior Aceite os termos do [plano de compra](#accept-purchase-plan-terms).

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Criar um novo VM a partir de um VHD com informações do plano de compra

Se tiver um VHD existente que foi criado usando uma imagem do Azure Marketplace, poderá ter de fornecer informações sobre o plano de compra quando criar um novo VM a partir desse VHD.

Se ainda tiver o VM original, ou outro VM criado a partir da mesma imagem, pode obter o nome do plano, editor e informações do produto usando o Get-AzVM. Este exemplo obtém um VM nomeado *myVM* no grupo de recursos *myResourceGroup* e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Se não tiver a informação do plano antes da vm original ser eliminada, pode apresentar um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support). Precisarão do nome VM, ID de subscrição e da hora da operação de eliminação.

Para criar um VM utilizando um VHD, consulte este artigo [Crie um VM a partir de um VHD especializado](create-vm-specialized.md) e adicione uma linha para adicionar as informações do plano à configuração VM utilizando o [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) semelhante ao seguinte:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Passos seguintes

Para criar uma máquina virtual rapidamente com o `New-AzVM` cmdlet utilizando informações básicas de imagem, consulte [Criar uma máquina virtual Windows com PowerShell](quick-create-powershell.md).

Para obter mais informações sobre a utilização de imagens do Azure Marketplace para criar imagens personalizadas numa galeria de imagens partilhada, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](../marketplace-images.md)
