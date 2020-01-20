---
title: Usar o runbook de automação do Azure para gerenciar dispositivos StorSimple
description: Saiba como usar o runbook de automação do Azure para automatizar trabalhos do StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276961"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Usar runbooks de automação do Azure para gerenciar dispositivos StorSimple

Este artigo descreve como os runbooks de automação do Azure são usados para gerenciar seu dispositivo StorSimple 8000 Series no portal do Azure. Um runbook de exemplo está incluído para orientá-lo pelas etapas de configuração do seu ambiente para executar esse runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Configurar, adicionar e executar o runbook do Azure

Esta seção usa um script do Windows PowerShell de exemplo para o StorSimple e detalha as várias etapas necessárias para importar o script para um runbook e, em seguida, publicar e executar o runbook.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

* uma assinatura ativa do Azure associada ao seu serviço StorSimple Device Manager registrada com um dispositivo StorSimple da série 8000.

* O Windows PowerShell 5,0 instalado em seu computador (ou o host do Windows Server para seu StorSimple, se estiver usando um).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Criar módulo de runbook de automação no Windows PowerShell

Para criar um módulo de automação para o gerenciamento de dispositivo StorSimple 8000 Series, execute as seguintes etapas:

1. Inicie o Windows PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Baixe a CLI do NuGet](https://www.nuget.org/downloads) na pasta criada na etapa anterior. Há várias versões do _NuGet. exe_. Escolha a versão correspondente ao seu SDK. Cada link de download aponta diretamente para um arquivo _. exe_ . Certifique-se de clicar com o botão direito do mouse e salvar o arquivo em seu computador em vez de executá-lo no navegador.

    Você também pode executar o comando a seguir para baixar e armazenar o script na mesma pasta que você criou anteriormente.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Baixe o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Baixe o script do projeto GitHub de exemplo.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Crie um módulo de runbook de automação do Azure para o gerenciamento de dispositivo do StorSimple 8000 Series. Na janela do Windows PowerShell, digite os seguintes comandos:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Verifique se um arquivo zip do módulo de automação foi criado no `C:\scripts\StorSimpleSDKTools`.

    ![verificar-Automation-Module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A saída a seguir é apresentada quando o módulo de automação é criado por meio do Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook de automação

1. Crie uma conta de automação executar como do Azure no portal do Azure. Para fazer isso, vá para o **Azure marketplace > tudo** e, em seguida, pesquise **automação**. Selecione **contas de automação**.

    ![pesquisa-automação](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Na folha **adicionar conta de automação** :

   1. Forneça o **nome** da sua conta de automação.
   2. Selecione a **assinatura** vinculada ao serviço de Device Manager do StorSimple.
   3. Crie um novo grupo de recursos ou selecione um grupo de recursos existente.
   4. Selecione um **local** (se possível, o mesmo que o serviço está em execução).
   5. Deixe a opção padrão **criar conta Executar como** selecionada.
   6. Opcionalmente, marque **fixar no painel**. Clique em **Criar**.

       ![criar conta de automação](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Depois que a conta de automação for criada com êxito, você será notificado. Para obter mais informações sobre como criar uma conta de automação, vá para [criar uma conta Executar como](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Para garantir que a conta de automação criada possa acessar o serviço de Device Manager do StorSimple, você precisa atribuir as permissões apropriadas à conta de automação. Acesse o **controle de acesso** em seu serviço StorSimple Device Manager. Clique em **+ Adicionar** e forneça o nome da sua conta de automação do Azure. **Salve** as configurações.

    ![Add-Permissions-Automation-Account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Na conta recém-criada, acesse **recursos compartilhados > módulos** e clique em **+ Adicionar módulo**.

5. Na folha **Adicionar módulo** , navegue até o local do módulo compactado e selecione e abra o módulo. Clique em **OK**.

    ![Adicionar módulo](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Vá para **processo de automação > Runbooks e clique em + Adicionar um runbook**. Na folha **Adicionar runbook** , clique em **importar um runbook existente**. Aponte para o arquivo de script do Windows PowerShell para o **arquivo de runbook**. O tipo de runbook é selecionado automaticamente. Forneça um nome e uma descrição opcional para o runbook. Clique em **Criar**.

    ![Adicionar módulo](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. O runbook é adicionado à lista de runbooks. Selecione e clique neste runbook.

    ![clique em novo runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edite o runbook e clique em **painel de teste**. Forneça os parâmetros, como o nome do serviço de Device Manager do StorSimple, o nome do dispositivo StorSimple e a assinatura. **Inicie** o teste. O relatório é gerado quando a execução é concluída. Para obter mais informações, acesse [como testar um runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![testar runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Inspecione a saída do runbook no painel de teste. Se estiver satisfeito, feche o painel. Clique em **publicar** e, quando for solicitado a confirmar, confirme e publique o runbook.

    ![publicar-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Passos seguintes

[Use o serviço de Device Manager do storsimple para gerenciar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).
