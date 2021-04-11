---
title: Image Builder - Crie uma imagem virtual do Windows desktop
description: Crie uma imagem Azure VM do Windows Virtual Desktop utilizando o Azure Image Builder em PowerShell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 69718b219d239ac13e5d932b05a7dd29619adaa3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045591"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Crie uma imagem virtual do Windows desktop utilizando o Azure VM Image Builder e o PowerShell

Este artigo mostra-lhe como criar uma imagem virtual do Windows desktop com estas personalizações:

* Instalação [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Executando um [script de otimização virtual do Windows Desktop](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) a partir do repo da comunidade.
* Instalar [equipas da Microsoft](../../virtual-desktop/teams-on-wvd.md).
* [Reiniciar](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* Executar [atualização do Windows](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer)

Vamos mostrar-lhe como automatizar isto usando o Azure VM Image Builder, e distribuir a imagem para uma [Galeria de Imagem Partilhada,](../shared-image-galleries.md)onde pode replicar-se para outras regiões, controlar a escala e partilhar a imagem dentro e fora das suas organizações.


Para simplificar a implementação de uma configuração do Image Builder, este exemplo utiliza um modelo de Gestor de Recursos Azure com o modelo de Construtor de Imagens aninhado no interior. Isto dá-lhe outros benefícios, como variáveis e entradas de parâmetros. Também pode passar parâmetros a partir da linha de comando.

Este artigo destina-se a ser um exercício de cópia e pasta.

> [!NOTE]
> Os scripts para instalar as aplicações estão localizados no [GitHub.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD) São apenas para ilustração e teste, e não para cargas de trabalho de produção. 

## <a name="tips-for-building-windows-images"></a>Dicas para construir imagens do Windows 

- Tamanho VM - o tamanho VM predefinido é um `Standard_D1_v2` , que não é adequado para Windows. Use um `Standard_D2_v2` ou maior.
- Este exemplo utiliza os [scripts personalizadores PowerShell](../linux/image-builder-json.md). Tem de utilizar estas definições ou a construção deixará de responder.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Por exemplo:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Comente o seu código - O registo de construção AIB (personalização.log) é extremamente verboso, se comentar os seus scripts usando 'write-host' estes serão enviados para os registos e facilitar a resolução de problemas.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Códigos de Saída - O AIB espera que todos os scripts devolvam um código de saída 0, qualquer código de saída não zero resultará em AIB falhar na personalização e parar a construção. Se tiver scripts complexos, adicionar instrumentação e emitir códigos de saída, estes serão mostrados na personalização.log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Teste: Por favor, teste e teste o seu código antes num VM autónomo, certifique-se de que não existem indicações do utilizador, está a utilizar o privilégio certo, etc.

- Networking - `Set-NetAdapterAdvancedProperty` . Isto está a ser definido no script de otimização, mas falha a construção do AIB, uma vez que desliga a rede, isto é comentado. Está sob investigação.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter os mais recentes CmdLets Azure PowerShell instalados, consulte [aqui](/powershell/azure/overview) para obter detalhes de instalação.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Configurar ambiente e variáveis

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Permissões, identidade e papel do utilizador 


 Criar uma identidade de utilizador.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Atribua permissões à identidade para distribuir imagens. Este comando irá descarregar e atualizar o modelo com os parâmetros especificados anteriormente.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Se vir este erro: 'New-AzRoleDefinition: Limite de definição de função ultrapassado. Não podem ser criadas mais definições de papel. ver este artigo para resolver: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagens Partilhadas 

Se ainda não tem uma Galeria de Imagens Partilhadas, tem de criar uma.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Configure o modelo de imagem

Para este exemplo, temos um modelo pronto para o qual irá descarregar e atualizar o modelo com os parâmetros especificados anteriormente, irá instalar FsLogix, otimizações de SO, Microsoft Teams e executar o Windows Update no final.

Se abrir o modelo pode ver na propriedade de origem a imagem que está a ser utilizada, neste exemplo utiliza uma imagem de sessão Win 10 Multi. 

### <a name="windows-10-images"></a>Imagens do Windows 10
Dois tipos-chave que deve estar atento: multisessão e sessão única.

As imagens de várias sessões destinam-se a uma utilização em conjunto. Aqui está um exemplo dos detalhes da imagem em Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

As imagens de sessão única são destinam-se a uma utilização individual. Aqui está um exemplo dos detalhes da imagem em Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Também pode alterar as imagens Win10 disponíveis:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Modelo de descarregamento e configuração

Agora, você precisa baixar o modelo e configurá-lo para o seu uso.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Sinta-se livre para ver o [modelo,](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)todo o código é visível.


## <a name="submit-the-template"></a>Submeta o modelo

O seu modelo deve ser submetido ao serviço, este irá descarregar quaisquer artefactos dependentes (como scripts), validar, verificar permissões e armazená-los no Grupo de Recursos de encenação, pré-fixado, *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Criar a imagem
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> O comando não esperará que o serviço de construção de imagens complete a construção de imagens, pode consultar o estado abaixo.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Criar uma VM
Agora que a construção está terminada pode construir um VM a partir da imagem, usar os exemplos [daqui.](/powershell/module/az.compute/new-azvm#examples)

## <a name="clean-up"></a>Limpeza

Elimine primeiro o modelo de grupo de recursos, não apenas elimine todo o grupo de recursos, caso contrário o grupo de recursos de preparação (*IT_*) utilizado pelo AIB não será limpo.

Remova o modelo de imagem.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Apague a atribuição de funções.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Elimine o grupo de recursos.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Pode experimentar mais exemplos [no GitHub.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)