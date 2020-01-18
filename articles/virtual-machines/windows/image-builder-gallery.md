---
title: Usar o construtor de imagens do Azure com uma galeria de imagens para VMs do Windows (versão prévia)
description: Crie versões de imagem da Galeria compartilhada do Azure usando o construtor de imagens do Azure e Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263358"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: criar uma imagem do Windows e distribuí-la para uma galeria de imagens compartilhadas 

Este artigo mostra como você pode usar o construtor de imagens do Azure e Azure PowerShell, para criar uma versão de imagem em uma [Galeria de imagens compartilhada](shared-image-galleries.md)e, em seguida, distribuir a imagem globalmente. Você também pode fazer isso usando o [CLI do Azure](../linux/image-builder-gallery.md).

Usaremos um modelo. JSON para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [armTemplateWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Baixaremos e editando uma versão local do modelo, para que este artigo seja escrito usando a sessão local do PowerShell.

Para distribuir a imagem a uma galeria de imagens compartilhadas, o modelo usa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) como o valor para a seção `distribute` do modelo.

O construtor de imagem do Azure executa automaticamente o Sysprep para generalizar a imagem, esse é um comando do Sysprep genérico, que você pode [substituir](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) , se necessário. 

Lembre-se de quantas vezes as personalizações em camadas. Você pode executar o comando Sysprep até 8 vezes em uma única imagem do Windows. Depois de executar o Sysprep 8 vezes, você deve recriar a imagem do Windows. Para obter mais informações, consulte [limites de quantas vezes você pode executar o Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> O construtor de imagem do Azure está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrar os recursos
Para usar o construtor de imagens do Azure durante a versão prévia, você precisa registrar o novo recurso.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Verifique o status do registro do recurso.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Aguarde até que `RegistrationState` seja `Registered` antes de passar para a próxima etapa.

Verifique os registros do provedor. Verifique se cada um retorna `Registered`.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se eles não retornarem `Registered`, use o seguinte para registrar os provedores:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Criar variáveis

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazenar essas informações. Substitua os valores das variáveis, como `username` e `vmpassword`, por suas próprias informações.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Crie um grupo de recursos e dê permissão ao construtor de imagens do Azure para criar recursos nesse grupo de recursos.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de imagens compartilhadas

Para usar o construtor de imagem com uma galeria de imagens compartilhada, você precisa ter uma galeria de imagens e uma definição de imagem existentes. O Image Builder não criará a Galeria de imagens e a definição de imagem para você.

Se você ainda não tiver uma definição de imagem e galeria para usar, comece criando-as. Primeiro, crie uma galeria de imagens.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Baixar e configurar o modelo

Baixe o modelo. JSON e configure-o com suas variáveis.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Criar a versão da imagem

Seu modelo deve ser enviado ao serviço, isso baixará quaisquer artefatos dependentes, como scripts, e os armazenará no grupo de recursos de preparo, prefixado com *IT_* .

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Para criar a imagem, você precisa invocar ' Run ' no modelo.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Criar a imagem e replicá-la para ambas as regiões pode levar algum tempo. Aguarde até que essa parte seja concluída antes de passar para a criação de uma VM.

Para obter informações sobre opções para automatizar a obtenção do status da compilação da imagem, consulte o [Leiame](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) para este modelo no github.


## <a name="create-the-vm"></a>Crie a VM

Crie uma VM com base na versão de imagem criada pelo construtor de imagens do Azure.

Obtenha a versão da imagem que você criou.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Crie a VM na segunda região em que a imagem foi replicada.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verificar a personalização
Crie uma conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Você deve ver um diretório chamado `buildActions` que foi criado durante a personalização da imagem.


## <a name="clean-up-resources"></a>Limpar recursos
Se você quiser agora tentar repersonalizar a versão da imagem para criar uma nova versão da mesma imagem, **pule esta etapa** e vá para [usar o construtor de imagem do Azure para criar outra versão da imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, junto com todos os outros arquivos de recurso. Verifique se você concluiu essa implantação antes de excluir os recursos.

Exclua o modelo de grupo de recursos primeiro, caso contrário, o grupo de recursos de preparo (*IT_* ) usado pelo AIB não será limpo.

Obter ResourceId do modelo de imagem. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Excluir modelo de imagem.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Exclua o grupo de recursos.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Próximos Passos

Para saber como atualizar a versão de imagem que você criou, consulte [usar o construtor de imagem do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).
