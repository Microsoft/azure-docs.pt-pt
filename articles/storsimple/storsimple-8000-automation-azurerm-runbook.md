---
title: Utilize o Livro de Execução de Automação Azure para gerir dispositivos StorSimple
description: Saiba como usar o Livro de Execução de Automação Azure para automatizar trabalhos StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276961"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Utilize livros de execução Da Automatização Azure para gerir dispositivos StorSimple

Este artigo descreve como os livros de execução da Azure Automation são usados para gerir o seu dispositivo da série StorSimple 8000 no portal Azure. Um livro de amostras está incluído para acompanhá-lo através dos passos de configurar o seu ambiente para executar este livro de execução.


## <a name="configure-add-and-run-azure-runbook"></a>Configure, adicione e executar o livro de corridas Azure

Esta secção tem um exemplo do script Windows PowerShell para o StorSimple e detalha os vários passos necessários para importar o script num livro de execução e depois publicar e executar o livro de execução.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

* uma subscrição ativa do Azure associada ao seu serviço StorSimple Device Manager registado com um dispositivo da série StorSimple 8000.

* O Windows PowerShell 5.0 instalado no seu computador (ou, o seu anfitrião do Windows Server para o seu StorSimple se utilizar um).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Crie módulo de livro de automação no Windows PowerShell

Para criar um módulo de automatização para a gestão do dispositivo da série StorSimple 8000, execute os seguintes passos:

1. Lançar o Windows PowerShell. Crie uma nova pasta e mude o diretório para a nova pasta.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Baixe nuGet CLI](https://www.nuget.org/downloads) sob a pasta criada no passo anterior. Existem várias versões de _nuget.exe._ Escolha a versão correspondente ao seu SDK. Cada link de descarregamento aponta diretamente para um ficheiro _.exe._ Certifique-se de clicar à direita e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.

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

5. Crie um Módulo de Rexecução de Automação Azure para a gestão de dispositivos da Série 8000 StorSimple 8000. Na janela Windows Powershell, escreva os seguintes comandos:

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

6. Verifique se é criado um `C:\scripts\StorSimpleSDKTools`ficheiro zip de módulo de automatização em .

    ![verificar módulo de automação](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A saída seguinte é apresentada quando o módulo de automação é criado através do Windows PowerShell.

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

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar livro de execução automation

1. Crie uma conta de automação Azure Run No portal Azure. Para isso, vá ao **mercado Azure > Everything** e, em seguida, procure a **Automação.** Selecione **contas de Automação**.

    ![automação de pesquisa](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Na lâmina **da Conta Add Automation:**

   1. Forneça o **nome** da sua conta de Automação.
   2. Selecione a **Subscrição** ligada ao seu serviço StorSimple Device Manager.
   3. Crie um novo grupo de recursos ou selecione a partir de um grupo de recursos existente.
   4. Selecione um **Local** (se possível o mesmo que o seu serviço está em funcionamento).
   5. Deixe o padrão **Criar Executar Como** opção de conta selecionada.
   6. Verifique opcionalmente **pin o painel de instrumentos**. Clique em **Criar**.

       ![criar-automação-conta](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Após a criação da conta de automação com sucesso, é notificado. Para mais informações sobre como criar uma conta De automatização, vá para [Criar uma conta Run As](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Para garantir que a conta de automação criada pode aceder ao serviço StorSimple Device Manager, é necessário atribuir permissões adequadas à conta de automação. Aceda ao **controlo de acesso** no serviço StorSimple Device Manager. Clique **+ Adicione** e forneça o nome da sua Conta de Automação Azure. **Guarde** as definições.

    ![adicionar permissões-conta de automação](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Na conta recém-criada, vá aos **Recursos Partilhados > Módulos** e clique **+ Adicionar módulo**.

5. Na lâmina do **módulo Adicionar,** navegue até à localização do módulo zipped e selecione e abra o módulo. Clique em **OK**.

    ![módulo adicional](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Vá ao **Process Automation > Runbooks e clique + Adicione um livro de execução**. Na lâmina **add runbook,** clique **em Importar um livro de execução existente**. Aponte para o ficheiro de script do Windows PowerShell para o **ficheiro 'Livro de Execução'.** O tipo de livro de execução é automaticamente selecionado. Forneça um nome e uma descrição opcional para o livro de execução. Clique em **Criar**.

    ![módulo adicional](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. O livro de execução é adicionado à lista de livros de execução. Selecione e clique neste livro de execução.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Editar o livro de execução e clicar no **painel de teste**. Forneça os parâmetros como o nome do seu serviço StorSimple Device Manager, o nome do dispositivo StorSimple e a subscrição. **Começa** o teste. O relatório é gerado quando a execução está completa. Para mais informações, vá testar um livro de [corridas](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![livro de ensaios](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Inspecione a saída do livro de ensaios no painel de ensaio. Se estiver satisfeito, feche o painel. Clique em **Publicar** e quando solicitado para confirmação, confirme e publique o livro de execução.

    ![livro de execução de publicações](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Passos seguintes

Utilize o [serviço StorSimple Device Manager para gerir o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
