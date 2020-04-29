---
title: Selecione imagens VM do Windows em Azure
description: Utilize o Azure PowerShell para determinar a editora, oferta, SKU e versão para imagens De Marketplace VM.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 46a2badbbe957f6a8a6af7f5a40633ea24cadcd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82083370"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Localizar imagens de VM do Windows no Azure Marketplace com o Azure PowerShell

Este artigo descreve como usar o Azure PowerShell para encontrar imagens VM no Azure Marketplace. Pode então especificar uma imagem do Marketplace quando criar um VM programáticamente com modelos PowerShell, Resource Manager ou outras ferramentas.

Também pode navegar em imagens e ofertas disponíveis utilizando a montra [Azure Marketplace,](https://azuremarketplace.microsoft.com/) o [portal Azure,](https://portal.azure.com)ou o [Azure CLI.](../linux/cli-ps-findimage.md) 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela de imagens do Windows comumente usadas

Esta tabela mostra um subconjunto de Skus disponíveis para as Editoras e Ofertas indicadas.

| Publicador | Oferta | Sku |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |Centro de Dados 2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |Centro de Dados 2016 |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |Rserver-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navegue nas imagens

Uma maneira de encontrar uma imagem num local é executar o [Get-AzVMImagePublisher,](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)e [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) cmdlets por ordem:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em seguida, para um SKU selecionado, executar [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) para listar as versões para implementar.

1. Lista rindo os editores:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Preencha o nome de editor escolhido e enumere as ofertas:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Preencha o nome da oferta escolhida e enumere as SKUs:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Preencha o nome SKU escolhido e obtenha a versão de imagem:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A partir da `Get-AzVMImage` saída do comando, pode selecionar uma imagem de versão para implantar uma nova máquina virtual.

O exemplo que se segue mostra a sequência completa de comandos e as suas saídas:

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

Para o editor do *MicrosoftWindowsServer:*

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Saída:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Para a oferta do *WindowsServer:*

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

Então, para o SKU *2019-Datacenter:*

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Agora pode combinar o editor selecionado, oferta, SKU e versão numa URN (valores separados por :). Passe este URN `--image` com o parâmetro quando criar um VM com o cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Pode substituir opcionalmente o número da versão na URN por "mais recente" para obter a versão mais recente da imagem.

Se implementar um VM com um modelo de Gestor de Recursos, `imageReference` então irá definir os parâmetros de imagem individualmente nas propriedades. Veja a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Ver propriedades do plano

Para ver a informação do plano `Get-AzVMImage` de compra de uma imagem, execute o cmdlet. Se `PurchasePlan` a propriedade na `null`saída não estiver, a imagem tem termos que precisa de aceitar antes da implementação programática.  

Por exemplo, a imagem do *Datacenter do Windows Server 2016* não tem termos adicionais, pelo que a `PurchasePlan` informação é: `null`

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Saída:

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

O exemplo abaixo mostra um comando semelhante para a Máquina Virtual da Ciência dos `name` `product`Dados `publisher`- imagem *do Windows 2016,* que tem as seguintes `PurchasePlan` propriedades: , e . Algumas imagens `promotion code` também têm uma propriedade. Para implementar esta imagem, consulte as seguintes secções para aceitar os termos e para permitir a implementação programática.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Saída:

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

Para visualizar os termos da licença, utilize o cmdlet [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passe nos parâmetros do plano de compra. A saída fornece uma ligação aos termos da imagem do Marketplace e mostra se aceitou previamente os termos. Certifique-se de que utiliza todas as letras minúsculas nos valores do parâmetro.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Saída:

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

Utilize os termos [set-AzMarketplace para](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) aceitar ou rejeitar os termos. Só precisa de aceitar termos uma vez por subscrição para a imagem. Certifique-se de que utiliza todas as letras minúsculas nos valores do parâmetro. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Saída:

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

### <a name="deploy-using-purchase-plan-parameters"></a>Implementar utilizando parâmetros do plano de compra

Depois de aceitar os termos para uma imagem, pode implementar um VM nessa subscrição. Como mostrado no seguinte corte, utilize o cmdlet [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) para definir as informações do plano Marketplace para o objeto VM. Para um script completo para criar definições de rede para o VM e completar a implementação, consulte os exemplos do [script PowerShell](powershell-samples.md).

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

Para criar rapidamente uma `New-AzVM` máquina virtual com o cmdlet utilizando informações básicas de imagem, consulte [Criar uma máquina virtual Windows com powerShell](quick-create-powershell.md).


Consulte um exemplo de script PowerShell para [criar uma máquina virtual totalmente configurada](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


