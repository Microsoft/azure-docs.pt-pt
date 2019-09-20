---
title: Criar funções Web e de trabalho do Azure para PHP
description: Um guia para criar funções Web e de trabalho do PHP em um serviço de nuvem do Azure e configurar o tempo de execução do PHP.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 82bb5f153a2c70d3b26f295925f8e48693bc49b9
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146876"
---
# <a name="create-php-web-and-worker-roles"></a>Criar funções de Web PHP e de trabalho

## <a name="overview"></a>Descrição geral

Este guia mostrará como criar funções de trabalho ou Web do PHP em um ambiente de desenvolvimento do Windows, escolher uma versão específica do PHP nas versões "internas" disponíveis, alterar a configuração do PHP, habilitar extensões e, por fim, implantar no Azure. Ele também descreve como configurar uma função Web ou de trabalho para usar um tempo de execução do PHP (com extensões e configurações personalizadas) que você fornece.

O Azure fornece três modelos de computação para executar aplicações: Azure App serviço, máquinas virtuais do Azure e serviços de nuvem do Azure. Todos os três modelos dão suporte ao PHP. Os serviços de nuvem, que incluem funções Web e de trabalho, fornecem *PaaS (plataforma como serviço)* . Em um serviço de nuvem, uma função Web fornece um servidor Web Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos Web front-end. Uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas, independentemente da interação do usuário ou da entrada.

Para obter mais informações sobre essas opções, consulte [Opções de Hospedagem de computação fornecidas pelo Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP

O [SDK do Azure para php](https://github.com/Azure/azure-sdk-for-php) consiste em vários componentes. Este artigo usará dois deles: Azure PowerShell e os emuladores do Azure. Esses dois componentes podem ser instalados por meio do Microsoft Web Platform Installer. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de serviços de nuvem

A primeira etapa na criação de uma função Web ou de trabalho do PHP é criar um projeto de serviço do Azure. um projeto de serviço do Azure serve como um contêiner lógico para funções Web e de trabalho e contém os arquivos de [definição de serviço (. csdef)] e [configuração de serviço (. cscfg)] do projeto.

Para criar um novo projeto de serviço do Azure, execute Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Esse comando criará um novo diretório (`myProject`) ao qual você pode adicionar funções Web e de trabalho.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções Web ou de trabalho do PHP

Para adicionar uma função Web PHP a um projeto, execute o seguinte comando de dentro do diretório raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> O `roleName` parâmetro é opcional. Se for omitido, o nome da função será gerado automaticamente. A primeira função Web criada será `WebRole1`, a segunda `WebRole2`será e assim por diante. A primeira função de trabalho criada será `WorkerRole1`, a segunda `WorkerRole2`será e assim por diante.
>
>

## <a name="specify-the-built-in-php-version"></a>Especificar a versão interna do PHP

Quando você adiciona uma função Web ou de trabalho do PHP a um projeto do, os arquivos de configuração do projeto são modificados para que o PHP seja instalado em cada instância da Web ou de trabalho do seu aplicativo quando ele é implantado. Para ver a versão do PHP que será instalada por padrão, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A saída do comando acima será semelhante ao que é mostrado abaixo. Neste exemplo, o `IsDefault` sinalizador é definido como `true` para PHP 5.3.17, indicando que será a versão padrão do PHP instalada.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Você pode definir a versão de tempo de execução do PHP para qualquer uma das versões do PHP listadas. Por exemplo, para definir a versão do PHP (para uma função com o `roleName`nome) como 5.4.0, use o seguinte comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> As versões do PHP disponíveis podem ser alteradas no futuro.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o tempo de execução do PHP interno

Você tem controle total sobre a configuração do tempo de execução do PHP que é instalada quando você segue as etapas acima, incluindo `php.ini` a modificação de configurações e a habilitação de extensões.

Para personalizar o tempo de execução do PHP interno, siga estas etapas:

1. Adicione uma nova pasta, denominada `php`, `bin` ao diretório da sua função Web. Para uma função de trabalho, adicione-a ao diretório raiz da função.
2. Na pasta, crie outra pasta chamada `ext`. `php` Coloque todos `.dll` os arquivos de extensão (por `php_mongo.dll`exemplo,) que você deseja habilitar nessa pasta.
3. Adicione um `php.ini` arquivo `php` à pasta. Habilite todas as extensões personalizadas e defina as diretivas do PHP neste arquivo. Por exemplo, se você quisesse ativar `display_errors` e habilitar a `php_mongo.dll` extensão `php.ini` , o conteúdo do arquivo seria o seguinte:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> As configurações que você não definir explicitamente no `php.ini` arquivo que você fornecer serão automaticamente definidas para seus valores padrão. No entanto, tenha em mente que você pode adicionar `php.ini` um arquivo completo.
>
>

## <a name="use-your-own-php-runtime"></a>Usar seu próprio tempo de execução do PHP

Em alguns casos, em vez de selecionar um tempo de execução interno do PHP e configurá-lo conforme descrito acima, talvez você queira fornecer seu próprio tempo de execução do PHP. Por exemplo, você pode usar o mesmo tempo de execução do PHP em uma função Web ou de trabalho que você usa em seu ambiente de desenvolvimento. Isso facilita a garantia de que o aplicativo não alterará o comportamento em seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função Web para usar seu próprio tempo de execução do PHP

Para configurar uma função Web para usar um tempo de execução do PHP que você fornece, siga estas etapas:

1. Crie um projeto de serviço do Azure e adicione uma função Web do PHP, conforme descrito anteriormente neste tópico.
2. Crie uma `php` pasta `bin` na pasta que está no diretório raiz da função Web e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas, `php` etc.) à pasta.
3. ADICIONAL Se o tempo de execução do PHP usar os [drivers da Microsoft para PHP para SQL Server][sqlsrv drivers], você precisará configurar sua função Web para instalar o [SQL Server Native Client 2012][sql native client] quando ele for provisionado. Para tal, adicione a [instalador sqlncli. msi x64] para o `bin` pasta no diretório de raiz da sua função da web. O script de inicialização descrito na próxima etapa executará silenciosamente o instalador quando a função for provisionada. Se o tempo de execução do PHP não usar os drivers da Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa de inicialização que configure [serviços de informações da Internet (IIS)][iis.net] para usar o tempo de execução do PHP para lidar `.php` com solicitações de páginas. Para fazer isso, abra o `setup_web.cmd` arquivo ( `bin` no arquivo do diretório raiz da função Web) em um editor de texto e substitua seu conteúdo pelo seguinte script:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adicione os arquivos do aplicativo ao diretório raiz da função Web. Esse será o diretório raiz do servidor Web.
6. Publique seu aplicativo conforme descrito na seção [publicar seu aplicativo](#publish-your-application) abaixo.

> [!NOTE]
> O `download.ps1` script ( `bin` na pasta do diretório raiz da função Web) pode ser excluído depois que você seguir as etapas descritas acima para usar seu próprio tempo de execução do php.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para usar seu próprio tempo de execução do PHP

Para configurar uma função de trabalho para usar um tempo de execução do PHP que você fornece, siga estas etapas:

1. Crie um projeto de serviço do Azure e adicione uma função de trabalho do PHP, conforme descrito anteriormente neste tópico.
2. Crie uma `php` pasta no diretório raiz da função de trabalho e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas, etc `php` .) à pasta.
3. ADICIONAL Se o tempo de execução do PHP usar [drivers da Microsoft para PHP para SQL Server][sqlsrv drivers], será necessário configurar sua função de trabalho para instalar o [SQL Server Native Client 2012][sql native client] quando ele for provisionado. Para fazer isso, adicione o [instalador sqlncli. msi x64] ao diretório raiz da função de trabalho. O script de inicialização descrito na próxima etapa executará silenciosamente o instalador quando a função for provisionada. Se o tempo de execução do PHP não usar os drivers da Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa de inicialização que adiciona `php.exe` o executável à variável de ambiente Path da função de trabalho quando a função é provisionada. Para fazer isso, abra o `setup_worker.cmd` arquivo (no diretório raiz da função de trabalho) em um editor de texto e substitua seu conteúdo pelo seguinte script:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adicione os arquivos do aplicativo ao diretório raiz da sua função de trabalho.
6. Publique seu aplicativo conforme descrito na seção [publicar seu aplicativo](#publish-your-application) abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Executar seu aplicativo nos emuladores de computação e armazenamento

Os emuladores do Azure fornecem um ambiente local no qual você pode testar seu aplicativo do Azure antes de implantá-lo na nuvem. Há algumas diferenças entre os emuladores e o ambiente do Azure. Para entender isso melhor, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).

Observe que você deve ter o PHP instalado localmente para usar o emulador de computação. O emulador de computação usará a instalação do PHP local para executar o aplicativo.

Para executar o projeto nos emuladores, execute o seguinte comando no diretório raiz do projeto:

    PS C:\MyProject> Start-AzureEmulator

Você verá uma saída semelhante a esta:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Você pode ver seu aplicativo em execução no emulador abrindo um navegador da Web e navegando até o endereço local mostrado na saída`http://127.0.0.1:81` (na saída de exemplo acima).

Para interromper os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar a aplicação

Para publicar seu aplicativo, primeiro você precisa importar as configurações de publicação usando o cmdlet [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) . Em seguida, você pode publicar seu aplicativo usando o cmdlet [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) . Para obter informações sobre como entrar, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [central de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição de serviço (. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração de serviço (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalador sqlncli. msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
