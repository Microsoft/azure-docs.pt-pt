---
title: Desconfiem da palavra-passe do Gestor de Recursos Azure no seu dispositivo GPU Azure Stack Edge Pro
description: Descreve definir a palavra-passe do Gestor de Recursos Azure no seu GPU Azure Stack Edge Pro usando a Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: a2167d573871481c99b755fc713e18543d74c5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602687"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Definir palavra-passe do Gestor de Recursos Azure no dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como definir a sua senha do Gestor de Recursos Azure. Tem de definir esta palavra-passe quando estiver [a ligar-se às APIs locais do dispositivo através do Gestor de Recursos Azure](azure-stack-edge-gpu-connect-resource-manager.md). 

<!--The procedure to set the password can be different depending upon whether you use the Azure portal or the PowerShell cmdlets. Each of these procedures is described in the following sections.-->


## <a name="reset-password-via-the-azure-portal"></a>Redefinir a palavra-passe através do portal Azure

1. No portal Azure, aceda ao recurso Azure Stack Edge que criou para gerir o seu dispositivo. 

2. Ir para **propriedades**. No painel direito, na barra de comandos, selecione **Repor palavra-passe do ARM Edge**. 

    ![Redefinir a senha de utilizador do EdgeARM 2](media/azure-stack-edge-gpu-set-azure-resource-manager-password/set-edgearm-password-2.png)

3. Na lâmina de senha do **utilizador Reset EdgeArm,** forneça uma palavra-passe para ligar ao seu dispositivo APIs local através do Gestor de Recursos Azure. Confirme a palavra-passe e **selecione Reset**.

    ![Redefinir a senha de utilizador do EdgeARM 3](media/azure-stack-edge-gpu-set-azure-resource-manager-password/set-edgearm-password-3.png)



<!--## Reset password via PowerShell

1. In the Azure Portal, go to the Azure Stack Edge resource you created to manage your device. Make a note of the following parameters in the **Overview** page.

    - Azure Stack Edge resource name
    - Subscription ID

2. Go to **Settings > Properties**. Make a note of the following parameters in the **Properties** page.

    - Resource group
    - CIK encryption key: Select view and then copy the **Encryption Key**.

    ![Get CIK encryption key](media/azure-stack-edge-gpu-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identify a password that you will use to connect to Azure Resource Manager.

4. Start the cloud shell. Select on the icon in the top right corner:

    ![Start cloud shell](media/azure-stack-edge-gpu-set-azure-resource-manager-password/start-cloud-shell.png) 

    Once the cloud shell has started, you may need to switch to PowerShell.

    ![Cloud shell](media/azure-stack-edge-gpu-set-azure-resource-manager-password/cloud-shell.png)   


5. Set context. Type:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Here is a sample output:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  If you have any old PS modules, you need to install those.

    `Remove-Module  Az.DataBoxEdge -force`

    Here is a sample output. In this example, there were no old modules to be installed.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Next set of commands will download and run a script to install PowerShell modules.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. In the next set of commands, you'll need to provide the resource name, resource group name, encryption key, and the password you identified in the previous step.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    The password and encryption key parameters must be passed as secure strings. Use the following cmdlets to convert the password and encryption key to secure strings.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Use the above generated secure strings as parameters in the Set-AzDataBoxEdgeUser cmdlet to reset the password. Use the same resource group that you used when creating the Azure Stack Edge Pro/Data Box Gateway resource.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Here is the sample output.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Use the new password to connect to Azure Resource Manager.-->

## <a name="next-steps"></a>Passos seguintes

[Ligue-se ao Gestor de Recursos Azure](azure-stack-edge-gpu-connect-resource-manager.md)
