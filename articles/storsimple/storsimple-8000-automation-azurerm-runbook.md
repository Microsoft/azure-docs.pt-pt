---
title: Utilizar o Runbook da automatização do Azure para gerir dispositivos StorSimple | Documentos da Microsoft
description: Saiba como utilizar o Runbook da automatização do Azure para automatizar tarefas de StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 93c77b5f678c4e6b3170d2c7612bef3f104f0b6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002590"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Utilizar runbooks de automatização do Azure para gerir os dispositivos do StorSimple

Este artigo descreve como os runbooks de automatização do Azure são utilizados para gerir o seu dispositivo da série StorSimple 8000 no portal do Azure. Um runbook de exemplo está incluído para orientá-lo pelos passos de configuração de seu ambiente para executar este runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Configurar, adicionar e executar o runbook do Azure

Esta secção assume um exemplo de script do Windows PowerShell para StorSimple e fornece detalhes sobre as diversas etapas necessárias para importar o script para um runbook e, em seguida, publicar e executar o runbook.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

* uma subscrição do Azure Active Directory associada ao seu serviço StorSimple Device Manager registado com um dispositivo da série StorSimple 8000.


* Windows PowerShell 5.0 instalado no seu computador (ou, o Windows Server do anfitrião para o seu StorSimple se utilizar uma).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Criar o módulo do runbook de automatização no Windows PowerShell

Para criar um módulo de automatização para a gestão de dispositivos de série StorSimple 8000, execute os seguintes passos:

1. Lançamento de Windows PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Transferir o CLI do NuGet](https://www.nuget.org/downloads) sob a pasta que criou no passo anterior. Existem várias versões do _nuget.exe_. Escolha a versão correspondente ao seu SDK. Cada ligação de transferência aponta diretamente para um _.exe_ ficheiro. Certifique-se de que com o botão direito e guarde o ficheiro para o seu computador, em vez de executá-lo a partir do browser.

    Também pode executar o seguinte comando para transferir e armazenar o script na mesma pasta que criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Baixe o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Transferir o script do projeto do GitHub de exemplo.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Crie um módulo de Runbook de automatização do Azure para gestão de dispositivos do StorSimple série 8000. Na janela do Windows Powershell, escreva os seguintes comandos:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Certifique-se de que é criado um ficheiro de zip do módulo de automatização no `C:\scripts\StorSimpleSDKTools`.

    ![verify-automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. O resultado seguinte é apresentado quando o módulo de automatização é criado através do Windows PowerShell. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook de automatização

1. Crie uma conta de automatização Run As do Azure no portal do Azure. Para tal, aceda a **do Azure marketplace > tudo** e, em seguida, procure **automatização**. Selecione **contas de automatização**.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Na **adicionar conta de automatização** painel:

   1. Forneça o **nome** da sua conta de automatização.
   2. Selecione o **subscrição** ligado ao seu serviço StorSimple Device Manager.
   3. Criar um novo grupo de recursos ou selecione um grupo de recursos existente.
   4. Selecione um **localização** (se for possível o mesmo que em que o serviço está em execução).
   5. Deixe a predefinição **criar conta Run as** opção selecionada.
   5. Opcionalmente, verifique **afixar ao dashboard**. Clique em **Criar**.

       ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Depois da conta de automatização é criada com êxito, será notificado. Para obter mais informações sobre como criar uma conta de automatização, aceda a [criar uma conta Run As](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Para garantir que a conta de automatização criada pode a aceder ao serviço de Gestor de dispositivos do StorSimple, terá de atribuir permissões adequadas para a conta de automatização. Aceda a **controlo de acesso** no seu serviço StorSimple Device Manager. Clique em **+ adicionar** e forneça o nome da sua conta de automatização do Azure. **Guardar** as definições.

    ![adicionar-permissões--conta de automatização](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Na conta recentemente criada, aceda a **recursos partilhados > módulos** e clique em **+ Adicionar módulo**.

5. Na **Adicionar módulo** painel, navegue até à localização do módulo zipado e selecione e abra o módulo. Clique em **OK**.

    ![Adicionar-module](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Aceda a **automatização de processos > Runbooks e clique em + Adicionar um runbook**. Na **adicionar runbook** painel, clique em **importar um runbook existente**. Aponte para o ficheiro de script do Windows PowerShell para o **ficheiros do Runbook**. O tipo de runbook é selecionado automaticamente. Forneça um nome e uma descrição opcional para o runbook. Clique em **Criar**.

    ![Adicionar-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. O runbook é adicionado à lista de runbooks. Selecione e clique neste runbook.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Editar o runbook e clique em **painel de teste**. Forneça os parâmetros como o nome do seu serviço StorSimple Device Manager, o nome do dispositivo StorSimple e a subscrição. **Iniciar** o teste. O relatório é gerado quando a execução estiver concluída. Para obter mais informações, aceda a [como testar um runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![runbook de teste](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Verificar a saída do runbook no painel de teste. Se estiver satisfeito, feche o painel. Clique em **publicar** e quando lhe for pedida confirmação, confirme e publicar o runbook.

    ![publish-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Passos Seguintes

[Serviço de utilização StorSimple Device Manager para gerir o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).