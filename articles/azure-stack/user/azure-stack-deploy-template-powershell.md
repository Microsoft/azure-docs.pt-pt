---
title: Implementar modelos com o PowerShell no Azure Stack | Documentos da Microsoft
description: Implemente um modelo para o Azure Stack com o PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d71df99096e58b3ac7adc920b91891b9a50cd6f7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479586"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Implementar um modelo para o Azure Stack com o PowerShell

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode utilizar o PowerShell para implementar os modelos Azure Resource Manager para o Azure Stack. Este artigo descreve como utilizar o PowerShell para implementar um modelo.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar cmdlets do AzureRM PowerShell

Este exemplo utiliza **AzureRM** cmdlets do PowerShell e um modelo armazenado no GitHub. O modelo cria uma máquina virtual do Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Antes de tentar neste exemplo, certifique-se de que [configurado PowerShell](azure-stack-powershell-configure-user.md) para um utilizador do Azure Stack.

1. Aceda a [ https://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) e localize o **101-simples-windows-vm** modelo. Guardar o modelo nesta localização: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Abra uma linha de comandos elevada do PowerShell.
3. Substitua `username` e `password` no script com o seu nome de utilizador e palavra-passe e, em seguida, execute o script seguinte:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >Sempre que executar este script, incrementar o valor da `$myNum` parâmetro para impedir a substituição de sua implementação.

4. Abra a selecione portal, do Azure Stack **navegue**e, em seguida, selecione **máquinas virtuais** para localizar a sua nova máquina virtual (**myDeployment001**).

## <a name="next-steps"></a>Passos Seguintes

- [Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
