---
title: Criar um Windows VM com Azure Image Builder usando PowerShell
description: Crie um VM Windows com o módulo PowerShell do construtor de imagens Azure.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693463"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Pré-visualização: Criar um Windows VM com Azure Image Builder utilizando PowerShell

Este artigo demonstra como pode criar uma imagem personalizada do Windows utilizando o módulo PowerShell do construtor de imagens Azure VM.

> [!CAUTION]
> O Azure Image Builder está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibilizada sem contrato de nível de serviço. Não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto os módulos PowerShell e **Az.ImageBuilder** e **Az.ManagedServiceIdentity** estão em pré-visualização, deve instalá-los separadamente utilizando o `Install-Module` cmdlet com o `AllowPrerelease` parâmetro. Uma vez que estes módulos PowerShell ficam geralmente disponíveis, eles tornam-se parte de futuras libertações de módulos Az PowerShell e disponíveis nativamente a partir de Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Funcionalidades de registo

Se esta for a sua primeira utilização usando o construtor de imagens Azure durante a pré-visualização, registe a nova funcionalidade **VirtualMachineTemplatePreview.**

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Verifique o estado do registo de funcionalidades.

> [!NOTE]
> O **Estado de Registo** pode estar no estado por `Registering` alguns minutos antes de mudar para `Registered` . Aguarde até que o estado seja **registado** antes de continuar.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registe os seguintes fornecedores de recursos para utilização com a sua subscrição Azure se ainda não estiverem registados.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.Network
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definir variáveis

Vai usar várias informações repetidamente. Criar variáveis para armazenar a informação.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Crie uma variável para o seu ID de subscrição Azure. Para confirmar que a `subscriptionID` variável contém o seu ID de subscrição, pode executar a segunda linha no exemplo seguinte.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome da `$imageResourceGroup` variável na região especificada na `$location` variável. Este grupo de recursos é utilizado para armazenar o artefacto do modelo de configuração de imagem e a imagem.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Crie a identidade do utilizador e desemote permissões de função definidas

Conceda permissões ao construtor de imagens Azure para criar imagens no grupo de recursos especificado usando o seguinte exemplo. Sem esta permissão, o processo de construção de imagens não será concluído com sucesso.

Criar variáveis para a definição de função e nomes de identidade. Estes valores devem ser únicos.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Criar uma identidade de utilizador.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Armazenar o recurso de identidade e os iDs principais em variáveis.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Atribuir permissões de identidade para distribuir imagens

Descarregue o ficheiro .json config e modifique-o com base nas definições definidas neste artigo.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Crie a definição de papel.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Conceda a definição de função ao diretor do serviço de construtores de imagens.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Se receber o erro: "_New-AzRoleDefinition: Limite de definição de função ultrapassado. Não podem ser criadas mais definições de papel._ [](../../role-based-access-control/troubleshooting.md)

## <a name="create-a-shared-image-gallery"></a>Criar um Shared Image Gallery

Crie a galeria.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Crie uma definição de galeria.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Criar uma imagem

Crie um objeto de origem de construtor de imagens Azure. Consulte [as imagens VM do Windows no Mercado Azure com a Azure PowerShell](./cli-ps-findimage.md) para obter valores de parâmetros válidos.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Crie um objeto de distribuidor de imagem Azure.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Crie um objeto de personalização do construtor de imagens Azure.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Crie um segundo objeto de personalização do construtor de imagens Azure.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Crie um modelo de construtor de imagens Azure.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Quando concluída, uma mensagem é devolvida e um modelo de configuração de construtor de imagens é criado no `$imageResourceGroup` .

Para determinar se o processo de criação do modelo foi bem sucedido, pode usar o seguinte exemplo.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Em segundo plano, o construtor de imagens também cria um grupo de recursos de encenação na sua subscrição. Este grupo de recursos é utilizado para a construção de imagens. Está no formato: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Não elimine diretamente o grupo de recursos de encenação. Elimine o artefacto do modelo de imagem, isto fará com que o grupo de recursos de encenação seja eliminado.

Se o serviço reportar uma falha durante a submissão do modelo de configuração de imagem:

- Ver [Resolução de Problemas Azure VM Image Build (AIB) Falhas](../linux/image-builder-troubleshoot.md).
- Elimine o modelo utilizando o seguinte exemplo antes de voltar a tentar.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Inicie a construção de imagem

Submeta a configuração de imagem para o serviço de construtor de imagens VM.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Aguarde que o processo de construção de imagem esteja concluído. Este passo pode levar até uma hora.

Se encontrar erros, [reveja falhas na Construção de Imagem Azure VM (AIB).](../linux/image-builder-troubleshoot.md)

## <a name="create-a-vm"></a>Criar uma VM

Guarde as credenciais de login para o VM numa variável. A senha deve ser complexa.

```azurepowershell-interactive
$Cred = Get-Credential
```

Crie o VM utilizando a imagem que criou.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Verifique as personalizações

Crie uma ligação de ambiente de trabalho remoto ao VM utilizando o nome de utilizador e a palavra-passe que definiu quando criou o VM. Dentro do VM, abra o PowerShell e corra `Get-Content` como mostrado no seguinte exemplo:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Deve ver a saída com base no conteúdo do ficheiro criado durante o processo de personalização da imagem.

```Output
Azure-Image-Builder-Was-Here
```

A partir da mesma sessão PowerShell, verifique se a segunda personalização foi concluída com sucesso, verificando a presença do ficheiro `c:\buildArtifacts\index.html` como mostrado no exemplo seguinte:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

O resultado deve ser uma lista de diretórios mostrando o ficheiro descarregado durante o processo de personalização da imagem.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, pode eliminá-los executando os seguintes exemplos.

### <a name="delete-the-image-builder-template"></a>Elimine o modelo de construtor de imagens

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Eliminar o grupo de recursos de imagem

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a [referência do modelo do construtor de imagem](../linux/image-builder-json.md).
