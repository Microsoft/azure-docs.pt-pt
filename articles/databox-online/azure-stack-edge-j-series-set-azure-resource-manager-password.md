---
title: Desconfiem da palavra-passe do Gestor de Recursos Azure no seu dispositivo GPU Azure Stack Edge Pro
description: Descreve como ligar-se ao Gestor de Recursos Azure em execução no seu GPU Azure Stack Edge Pro utilizando o Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: df5fea8101834dae089ab97354c438363321a707
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904489"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Definir palavra-passe do Gestor de Recursos Azure no dispositivo GPU Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como definir a sua senha do Gestor de Recursos Azure. Tem de definir esta palavra-passe quando estiver a ligar-se às APIs locais do dispositivo através do Gestor de Recursos Azure.

O procedimento para definir a palavra-passe pode ser diferente dependendo se utiliza o portal Azure ou os cmdlets PowerShell. Cada um destes procedimentos é descrito nas seguintes secções.


## <a name="reset-password-via-the-azure-portal"></a>Redefinir a palavra-passe através do portal Azure

1. No portal Azure, aceda ao recurso Azure Stack Edge que criou para gerir o seu dispositivo. Vá ao **Edge compute > Começar**.

2. No painel direito, a partir da barra de comando, selecione **reset Edge ARM password**. 

    ![Redefinir a palavra-passe do utilizador EdgeARM 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. Na lâmina de senha do **utilizador Reset EdgeArm,** forneça uma palavra-passe para ligar ao seu dispositivo APIs local através do Gestor de Recursos Azure. Confirme a palavra-passe e **selecione Reset**.

    ![Redefinir a senha de utilizador do EdgeARM 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Redefinir a palavra-passe via PowerShell

1. No Portal Azure, aceda ao recurso Azure Stack Edge que criou para gerir o seu dispositivo. Tome nota dos seguintes parâmetros na página **'Vista Geral'.**

    - Nome de recurso Azure Stack Edge
    - ID da subscrição

2. Ir para **Configurações > Propriedades**. Tome nota dos seguintes parâmetros na página **Propriedades.**

    - Grupo de recursos
    - Chave de encriptação CIK: Selecione a vista e, em seguida, copie a **chave de encriptação**.

    ![Obtenha a chave de encriptação CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identifique uma palavra-passe que utilizará para ligar ao Gestor de Recursos Azure.

4. Inicie a casca da nuvem. Selecione no ícone no canto superior direito:

    ![Iniciar casca de nuvem](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Uma vez iniciada a casca de nuvem, poderá ter de mudar para PowerShell.

    ![Cloud shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Contexto definido. Escreva:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Eis uma saída de exemplo:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Se tiver algum módulo PS antigo, tem de os instalar.

    `Remove-Module  Az.DataBoxEdge -force`

    Aqui está uma amostra de saída. Neste exemplo, não havia módulos antigos para serem instalados.

    
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

6. O próximo conjunto de comandos irá descarregar e executar um script para instalar módulos PowerShell.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. No próximo conjunto de comandos, terá de fornecer o nome do recurso, o nome do grupo de recursos, a chave de encriptação e a palavra-passe que identificou no passo anterior.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    A palavra-passe e os parâmetros das chaves de encriptação devem ser passados como cadeias seguras. Utilize os seguintes cmdlets para converter a palavra-passe e a chave de encriptação para fixar as cordas.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Utilize as cordas de segurança acima geradas como parâmetros no Set-AzDataBoxEdgeUser cmdlet para redefinir a palavra-passe. Utilize o mesmo grupo de recursos que utilizou ao criar o recurso Azure Stack Edge Pro/Data Box Gateway.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Aqui está a saída da amostra.
    
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
Utilize a nova palavra-passe para ligar ao Gestor de Recursos Azure.

## <a name="next-steps"></a>Passos seguintes

[Ligue-se ao Gestor de Recursos Azure](azure-stack-edge-j-series-connect-resource-manager.md)
