---
title: Utilize o Runbook Azure Automation para gerir dispositivos StorSimple
description: Saiba como utilizar o Azure Automation Runbook para gerir o seu dispositivo da série StorSimple 8000 no portal Azure.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021083"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Utilize os runbooks da Azure Automation para gerir dispositivos StorSimple

Este artigo descreve como os runbooks da Azure Automation são utilizados para gerir o seu dispositivo da série StorSimple 8000 no portal Azure. Um livro de amostras está incluído para o acompanhar através dos passos de configurar o seu ambiente para executar este runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Configurar, adicionar e executar o runbook Azure

Esta secção toma um exemplo do script Do Windows PowerShell para StorSimple e detalha os vários passos necessários para importar o script num livro de recortes e, em seguida, publicar e executar o runbook.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

* uma subscrição ativa da Azure associada ao seu serviço StorSimple Device Manager registado com um dispositivo da série StorSimple 8000.

* Windows PowerShell 5.0 instalado no seu computador (Ou, o anfitrião do Servidor do Windows para o seu StorSimple se utilizar um).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Criar módulo de runbook de automatização no Windows PowerShell

Para criar um módulo de automatização para a gestão de dispositivos da série StorSimple 8000, execute os seguintes passos:

1. Lançamento do Windows PowerShell. Crie uma nova pasta e mude o diretório para a nova pasta.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Descarregue o NuGet CLI](https://www.nuget.org/downloads) sob a pasta criada no passo anterior. Existem várias versões de _nuget.exe._ Escolha a versão correspondente ao seu SDK. Cada link de descarregamento aponta diretamente para um ficheiro _.exe._ Certifique-se de clicar à direita e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.

    Também pode executar o seguinte comando para descarregar e armazenar o script na mesma pasta que criou anteriormente.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Descarregue o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Descarregue o script do projeto GitHub da amostra.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Crie um módulo runbook Azure Automation para gestão de dispositivos StorSimple 8000 Series. Na janela Windows Powershell, digite os seguintes comandos:

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

6. Verifique se um ficheiro zip do módulo de automatização é criado em `C:\scripts\StorSimpleSDKTools` .

    ![A screenshot mostra a pasta StorSimpleSDKTools com o ficheiro zip do módulo de automatização](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A seguinte saída é apresentada quando o módulo de automatização é criado através do Windows PowerShell.

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

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook automation

1. Crie uma conta Azure Run Como conta de automação no portal Azure. Para tal, vá ao **mercado Azure > Tudo** e depois procure por **Automação.** **Selecione contas de automação**.

    ![automação de pesquisa](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Na lâmina da **Conta Ded automatização adicionar:**

   1. Forneça o **Nome** da sua conta de Automação.
   2. Selecione a **Subscrição** ligada ao seu serviço StorSimple Device Manager.
   3. Crie um novo grupo de recursos ou selecione a partir de um grupo de recursos existente.
   4. Selecione uma **Localização** (se possível, o mesmo que o seu serviço está a funcionar).
   5. Deixe a opção **de execução por** defeito Como a opção de conta selecionada.
   6. Verifique opcionalmente **pin para painel de instrumentos**. Clique em **Criar**.

       ![Screenshot mostra o painel de conta de automação adicionar com valores para subscrição, criar Azure Run como conta e Criar 2](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Depois de a conta de automação ter sido criada com sucesso, é notificado. Para obter mais informações sobre como criar uma conta Demôm automação, vá a [Criar uma conta Run As](../automation/manage-runas-account.md).

3. Para garantir que a conta de automação criada pode aceder ao serviço StorSimple Device Manager, é necessário atribuir permissões adequadas à conta de automação. Aceda ao **controlo de acesso** no seu serviço StorSimple Device Manager. Clique **+ Adicione** e forneça o nome da sua Conta de Automação Azure. **Guarde** as definições.

    ![adicionar permissões-conta de automação](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Na conta recém-criada, vá a **Recursos Partilhados > Módulos** e clique **+ Adicionar módulo.**

5. Na lâmina do **módulo Adicionar,** navegue até à localização do módulo com fecho e selecione e abra o módulo. Clique em **OK**.

    ![A screenshot mostra o painel do Módulo de Adicionar com um valor de ficheiro de upload e OK](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Vá a **Processos de Automatização > Runbooks e clique + Adicione um runbook**. Na lâmina do **runbook Add,** clique em **Importar um livro de execução existente**. Aponte para o ficheiro de scripts Do Windows PowerShell para o **ficheiro Runbook**. O tipo de livro de execução é selecionado automaticamente. Forneça um nome e uma descrição opcional para o livro de recortes. Clique em **Criar**.

    ![módulo de adicionar](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. O livro de recortes é adicionado à lista de runbooks. Selecione e clique neste livro de execução.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edite o runbook e clique no **painel de teste**. Forneça os parâmetros como o nome do seu serviço StorSimple Device Manager, o nome do dispositivo StorSimple e a subscrição. **Começa** o teste. O relatório é gerado quando a execução está completa. Para mais informações, aceda a [como testar um livro de recortes.](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![A screenshot mostra a panela de teste onde pode fazer valores de parâmetros e iniciar um teste](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Inspecione a saída do livro de ensaios no painel de ensaio. Se estiver satisfeito, feche o painel. Clique **em Publicar** e quando solicitado para confirmação, confirme e publique o livro de recortes.

    ![A screenshot mostra o painel de runbook PowerShell de Edição que lhe leva a publicar o runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Passos seguintes

[Utilize o serviço StorSimple Device Manager para gerir o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).