---
title: Selecione as imagens de VM do Windows no Azure | Documentos da Microsoft
description: Utilize o Azure PowerShell para determinar o publicador, oferta, SKU e versão para imagens de VM do Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: danlep
ms.openlocfilehash: 1b3f000352a91d0204469f4ac14d5293140b933f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868855"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Encontrar imagens de VM do Windows no Azure Marketplace com o Azure PowerShell

Este artigo descreve como utilizar o Azure PowerShell para localizar imagens da VM no Azure Marketplace. Em seguida, pode especificar uma imagem do Marketplace quando criar uma VM através de programação com o PowerShell, modelos do Resource Manager ou outras ferramentas.

Também pode procurar imagens disponíveis e as ofertas com o [do Azure Marketplace](https://azuremarketplace.microsoft.com/) loja, o [portal do Azure](https://portal.azure.com), ou o [da CLI do Azure](../linux/cli-ps-findimage.md). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela de usadas imagens do Windows

Esta tabela mostra um subconjunto de Skus disponíveis para os publicadores e ofertas indicado.

| Publicador | Oferta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Navegar pelas imagens

Uma forma de localizar uma imagem num local está a executar o [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer), e [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) cmdlets na ordem:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em seguida, para uma SKU selecionado, execute [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) para listar as versões para implementar.

1. Liste os publicadores:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Preencha o nome do publicador escolhido e listar as ofertas de:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Preencha o nome da oferta escolhida e listar os SKUs:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```

4. Preencha o nome SKU escolhido e obtenha a versão da imagem:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A partir da saída do `Get-AzVMImage` de comando, pode selecionar uma imagem de versão para implementar uma nova máquina virtual.

O exemplo seguinte mostra a sequência completa de comandos e suas saídas:

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

Para o *MicrosoftWindowsServer* publicador:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Saída:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Para o *WindowsServer* oferecem:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
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

Em seguida, para o *2019 Datacenter* SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Agora pode combinar o selecionado do publicador, oferta, SKU e versão para um URN (valores separados por:). Passar este URN com o `--image` parâmetro ao criar uma VM com o [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet. Opcionalmente, pode substituir o número de versão a URN por "mais recente" para obter a versão mais recente da imagem.

Se implementar uma VM com um modelo do Resource Manager, em seguida, defina os parâmetros de imagem individualmente no `imageReference` propriedades. Veja a [referência do modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Ver as propriedades do plano

Para ver informações de plano de compra de uma imagem, execute o `Get-AzVMImage` cmdlet. Se o `PurchasePlan` propriedade na saída não é `null`, a imagem tem termos tem de aceitar antes de implementação programática.  

Por exemplo, o *Windows Server 2016 Datacenter* imagem não tiver termos adicionais, pelo que a `PurchasePlan` informações são `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
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

O exemplo abaixo mostra um comando semelhante para o *máquina de Virtual de ciência de dados - Windows 2016* imagem, que tem o seguinte procedimento `PurchasePlan` propriedades: `name`, `product`, e `publisher`. Algumas imagens também tem um `promotion code` propriedade. Para implementar esta imagem, veja as secções seguintes para aceitar os termos de e para ativar a implementação programática.

```powershell
Get-AzVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
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

Para ver os termos de licença, utilize o [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) plano parâmetros do cmdlet e passe a compra. A saída fornece uma ligação para os termos para a imagem do Marketplace e mostra se tiver aceitado os termos. Certifique-se de que utilize só letras minúsculas os valores de parâmetro.

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

Utilize o [Set-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) cmdlet para aceitar ou rejeitar os termos. Só tem de aceitar os termos de uma vez por subscrição para a imagem. Certifique-se de que utilize só letras minúsculas os valores de parâmetro. 

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

### <a name="deploy-using-purchase-plan-parameters"></a>Implementar com parâmetros de plano de compra

Depois de aceitar os termos de uma imagem, pode implementar uma VM nessa subscrição. Conforme mostrado no seguinte fragmento, utilize o [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) cmdlet para definir as informações de plano de Marketplace para o objeto VM. Para obter um script completo criar definições de rede para a VM e concluir a implementação, consulte a [exemplos de script do PowerShell](powershell-samples.md).

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
Em seguida, irá passar a configuração da VM, juntamente com objetos de configuração de rede para o `New-AzVM` cmdlet.

## <a name="next-steps"></a>Passos Seguintes

Para criar uma máquina virtual rapidamente com o `New-AzVM` cmdlet com informações de imagem básica, consulte [criar uma máquina virtual do Windows com o PowerShell](quick-create-powershell.md).


Veja um exemplo de script do PowerShell para [criar uma máquina virtual totalmente configurada](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


