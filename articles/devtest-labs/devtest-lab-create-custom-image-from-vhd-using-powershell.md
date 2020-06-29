---
title: Crie uma imagem personalizada a partir de ficheiro VHD usando Azure PowerShell
description: Automatizar a criação de uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o PowerShell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ae2a6b6b2f904915a51b94ad931d6c97996d27b
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483997"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Crie uma imagem personalizada a partir de um ficheiro VHD usando o PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos acompanham-no através da criação de uma imagem personalizada a partir de um ficheiro VHD utilizando o PowerShell:

1. Num pedido powerShell, inicie sessão na sua conta Azure com a seguinte chamada para o cmdlet **Connect-AzAccount.**

    ```powershell
    Connect-AzAccount
    ```

1.  Selecione a subscrição Azure desejada chamando o **cmdlet Select-AzSubscription.** Substitua o seguinte espaço reservado para a variável **$subscriptionId** por um ID de subscrição Azure válido.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Pegue o objeto do laboratório chamando o **cmdlet Get-AzResource.** Substitua os seguintes espaços reservados para as variáveis **$labRg** e **$labName** pelos valores adequados para o seu ambiente.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Substitua o seguinte espaço reservado para a variável **$vhdUri** com o URI no seu ficheiro VHD carregado. Pode obter o URI do ficheiro VHD da lâmina blob da conta de armazenamento no portal Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Crie a imagem personalizada utilizando o **cmdlet New-AzResourceGroupDeployment.** Substitua os seguintes espaços reservados para as variáveis **$customImageName** e **$customImageDescription** em nomes significativos para o seu ambiente.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script PowerShell para criar uma imagem personalizada a partir de um ficheiro VHD

O seguinte script PowerShell pode ser usado para criar uma imagem personalizada a partir de um ficheiro VHD. Substitua os espaços reservados (iniciando e terminando com suportes angulares) pelos valores adequados para as suas necessidades.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Publicações de blogs relacionadas

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)
