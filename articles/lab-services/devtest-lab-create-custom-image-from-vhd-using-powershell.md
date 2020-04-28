---
title: Criar uma imagem personalizada a partir do ficheiro VHD usando o Azure PowerShell
description: Automatizar a criação de uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando powerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: cd144659dd8a8e981e267be998c9c783b7482840
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169579"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Criar uma imagem personalizada a partir de um ficheiro VHD usando powerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por você através da criação de uma imagem personalizada a partir de um ficheiro VHD usando powerShell:

1. Numa solicitação powerShell, inicie sessão na sua conta Azure com a seguinte chamada para o cmdlet **Connect-AzAccount.**

    ```powershell
    Connect-AzAccount
    ```

1.  Selecione a subscrição azure desejada, ligando para o cmdlet **Select-AzSubscription.** Substitua o seguinte espaço reservado para a **variável $subscriptionId** por um ID de subscrição Azure válido.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Pegue o objeto de laboratório chamando o cmdlet **Get-AzResource.** Substitua os seguintes espaços reservados para as variáveis **$labRg** e **$labName** pelos valores adequados para o seu ambiente.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Substitua o seguinte espaço reservado para a **variável $vhdUri** com o URI no ficheiro VHD carregado. Pode obter o URI do ficheiro VHD da lâmina blob da conta de armazenamento no portal Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Crie a imagem personalizada utilizando o cmdlet **New-AzResourceGroupDeployment.** Substitua os seguintes espaços reservados para as **variáveis $customImageName** e **$customImageDescription** para nomes significativos para o seu ambiente.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script PowerShell para criar uma imagem personalizada a partir de um ficheiro VHD

O seguinte script PowerShell pode ser usado para criar uma imagem personalizada a partir de um ficheiro VHD. Substitua os espaços reservados (começando e terminando com parênteses angulares) pelos valores adequados às suas necessidades.

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

## <a name="related-blog-posts"></a>Posts de blog relacionados

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre laboratórios Azure DevTest](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)
