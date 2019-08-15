---
title: Criar um Azure DevTest Labs imagem personalizada de um arquivo VHD usando o PowerShell | Microsoft Docs
description: Automatizar a criação de uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: b43dc668af74f532838dad3baf1d6e11d51ac69d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964088"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Criar uma imagem personalizada de um arquivo VHD usando o PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Informações passo a passo

As etapas a seguir orientarão você na criação de uma imagem personalizada de um arquivo VHD usando o PowerShell:

1. Em um prompt do PowerShell, faça logon em sua conta do Azure com a seguinte chamada para o cmdlet **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Selecione a assinatura do Azure desejada chamando o cmdlet **Select-AzSubscription** . Substitua o espaço reservado a seguir para a variável **$SubscriptionId** com uma ID de assinatura do Azure válida.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Obtenha o objeto de laboratório chamando o cmdlet **Get-AzResource** . Substitua os espaços reservados a seguir para as variáveis **$labRg** e **$labName** com os valores apropriados para o seu ambiente.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Substitua o espaço reservado a seguir para a variável **$vhdUri** com o URI para o arquivo VHD carregado. Você pode obter o URI do arquivo VHD na folha BLOB da conta de armazenamento no portal do Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Crie a imagem personalizada usando o cmdlet **New-AzResourceGroupDeployment** . Substitua os espaços reservados a seguir para o **$customImageName** e **$customImageDescription** variáveis para nomes significativos para o seu ambiente.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script do PowerShell para criar uma imagem personalizada de um arquivo VHD

O script do PowerShell a seguir pode ser usado para criar uma imagem personalizada de um arquivo VHD. Substitua os espaços reservados (iniciando e terminando por colchetes angulares) pelos valores apropriados para suas necessidades.

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

## <a name="related-blog-posts"></a>Postagens de blog relacionadas

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiando imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md)
