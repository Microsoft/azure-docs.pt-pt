---
title: Localizar e utilizar imagens do Azure Marketplace
description: Utilize a Azure PowerShell para determinar a editora, oferta, SKU e versão para imagens Marketplace VM.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 96b5e3770a3f5e08237d61eab05cfeafbc72a5db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288347"
---
# <a name="find-and-use-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Localizar e utilizar imagens VM no Mercado Azure com Azure PowerShell

Este artigo descreve como usar a Azure PowerShell para encontrar imagens VM no Mercado Azure. Em seguida, pode especificar uma imagem do Marketplace quando criar um VM.

Também pode navegar nas imagens e ofertas disponíveis utilizando a montra do [Azure Marketplace,](https://azuremarketplace.microsoft.com/) o [portal Azure,](https://portal.azure.com)ou o [Azure CLI.](../linux/cli-ps-findimage.md) 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela de imagens do Windows comumente usadas

Esta tabela mostra um subconjunto de Skus disponíveis para os Editores e Ofertas Indicados.

| Publisher | Oferta | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |Centro de Dados 2019 |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |Centro de Dados de 2016 |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Grandes Empresas |
| MicrosoftRServer |RServer-WS2016 |Grandes Empresas |

## <a name="navigate-the-images"></a>Navegue pelas imagens

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

### <a name="deploy-using-purchase-plan-parameters"></a>Implementar usando parâmetros do plano de compra

Depois de aceitar os termos para uma imagem, pode implementar um VM nessa subscrição. Como mostrado no seguinte corte, utilize o [cmdlet Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) para definir as informações do plano marketplace para o objeto VM. Para obter um script completo para criar definições de rede para o VM e completar a implementação, consulte os exemplos de [script powerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Em seguida, passará a configuração VM juntamente com objetos de configuração de rede para o `New-AzVM` cmdlet.

## <a name="next-steps"></a>Passos seguintes

Para criar uma máquina virtual rapidamente com o `New-AzVM` cmdlet utilizando informações básicas de imagem, consulte [Criar uma máquina virtual Windows com PowerShell](quick-create-powershell.md).

Para obter mais informações sobre a utilização de imagens do Azure Marketplace para criar imagens personalizadas numa galeria de imagens partilhada, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](../marketplace-images.md)
