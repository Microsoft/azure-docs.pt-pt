---
title: Criar funções de web e de trabalho do Azure para PHP
description: Um guia para criar funções de web e de trabalho do PHP no serviço cloud do Azure e a configuração de tempo de execução do PHP.
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
ms.openlocfilehash: 83834104dd73e4381947903196ad35c3497b64a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337566"
---
# <a name="create-php-web-and-worker-roles"></a>Criar funções de Web PHP e de trabalho

## <a name="overview"></a>Descrição geral

Este guia irá mostrar como criar funções de web ou de trabalho PHP num ambiente de desenvolvimento do Windows, escolha uma versão específica do PHP entre as versões "internas" disponíveis, alterar a configuração do PHP, ativar extensões e, por fim, implementar no Azure. Também descreve como configurar uma função web ou de trabalho para utilizar um runtime PHP (com e extensões de configuração personalizada) que fornece.

O Azure fornece três modelos de computação para executar aplicações: Serviço de aplicações do Azure, máquinas virtuais do Azure e serviços Cloud do Azure. Todos os três modelos suportam o PHP. Os serviços cloud, incluindo funções web e de trabalho, proporcionam *plataforma como serviço (PaaS)*. Num serviço cloud, uma função da web fornece um servidor de web de serviços de informação Internet (IIS) dedicado para alojar aplicações web de front-end. Uma função de trabalho pode executar tarefas assíncronas, longa ou perpétuas, independentes da interação do utilizador ou de uma entrada.

Para obter mais informações sobre estas opções, consulte [computação fornecidas pelo Azure de opções de hospedagem](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP

O [Azure SDK para PHP](php-download-sdk.md) é composta por vários componentes. Este artigo irá utilizar dois deles: O Azure PowerShell e os emuladores do Azure. Esses dois componentes podem ser instalados através do Microsoft Web Platform Installer. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de serviços Cloud

É o primeiro passo na criação de uma função web ou de trabalho PHP criar um projeto de serviço do Azure. um projeto de serviço do Azure funciona como um contentor lógico de funções web e de trabalho e contém o projeto [definição (. csdef) do serviço] e [configuração de serviço (. cscfg)] ficheiros.

Para criar um novo projeto de serviço do Azure, execute o Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Este comando cria um novo diretório (`myProject`) ao qual pode adicionar funções web e de trabalho.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções de web ou de trabalho PHP

Para adicionar uma função da web PHP para um projeto, execute o comando seguinte a partir de diretório de raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> O `roleName` parâmetro é opcional. Se for omitida, o nome da função será gerado automaticamente. A primeira função da web criada serão `WebRole1`, a segunda `WebRole2`e assim por diante. A primeira função de trabalho criada será `WorkerRole1`, a segunda `WorkerRole2`e assim por diante.
>
>

## <a name="specify-the-built-in-php-version"></a>Especifique a versão PHP incorporada

Quando adiciona uma função web ou de trabalho PHP para um projeto, arquivos de configuração do projeto são modificados para que o PHP será instalado em cada instância de web ou de trabalho da sua aplicação quando for implementada. Para ver a versão do PHP que será instalada por predefinição, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A saída do comando acima terá um aspeto semelhante ao que é mostrado abaixo. Neste exemplo, o `IsDefault` sinalizador estiver definido como `true` para PHP 5.3.17, que indica que é a versão PHP predefinido instalada.

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

Pode definir a versão de runtime PHP para qualquer uma das versões do PHP que estão listadas. Por exemplo, para definir a versão do PHP (para uma função com o nome `roleName`) para 5.4.0, utilize o seguinte comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Versões do PHP disponíveis podem ser alterados no futuro.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o tempo de execução interno do PHP

Tem controle completo sobre a configuração do runtime PHP que é instalado quando seguir os passos acima, incluindo a modificação do `php.ini` definições e a ativação das extensões.

Para personalizar o tempo de execução interno do PHP, siga estes passos:

1. Adicionar uma nova pasta chamada `php`para o `bin` diretório da sua função da web. Para uma função de trabalho, adicione-o para o diretório de raiz da função.
2. Na `php` pasta, criar outra pasta chamada `ext`. Coloque qualquer `.dll` ficheiros de extensão (por exemplo, `php_mongo.dll`) que pretende ativar nesta pasta.
3. Adicionar uma `php.ini` do ficheiro para o `php` pasta. Ativar extensões personalizadas e defina qualquer diretivas PHP neste ficheiro. Por exemplo, se quiser transformar `display_errors` no e ativar o `php_mongo.dll` extensão, o conteúdo do seu `php.ini` ficheiro seria da seguinte forma:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Quaisquer definições que não for definida explicitamente no `php.ini` ficheiros que fornecem serão automaticamente ser definido para os valores predefinidos. No entanto, tenha em atenção que pode adicionar uma completa `php.ini` ficheiro.
>
>

## <a name="use-your-own-php-runtime"></a>Utilizar o seu próprio tempo de execução do PHP

Em alguns casos, em vez de selecionar um runtime PHP integrado e configurá-lo, conforme descrito acima, pode querer fornecer seu próprio tempo de execução do PHP. Por exemplo, pode utilizar o mesmo runtime PHP numa função web ou de trabalho que utiliza no seu ambiente de desenvolvimento. Isto torna mais fácil para se certificar de que a aplicação não será alterado o comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função da web para utilizar o seu próprio tempo de execução do PHP

Para configurar uma função da web para utilizar um runtime PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicionar uma função da web PHP, conforme descrito anteriormente neste tópico.
2. Criar uma `php` pasta na `bin` pasta que está no diretório de raiz da sua função da web e, em seguida, adicione o seu tempo de execução do PHP (todos os binários, arquivos de configuração, as subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se o runtime PHP utiliza a [Drivers Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a função da web para instalar [SQL Server Native Client 2012] [ sql native client] quando está aprovisionado. Para tal, adicione a [instalador de sqlncli.msi x64] para o `bin` pasta no diretório de raiz da sua função da web. O script de inicialização descrito no próximo passo será silenciosamente execute o instalador, quando a função está aprovisionada. Se o runtime PHP não utiliza os Drivers Microsoft para PHP para SQL Server, pode remover a seguinte linha do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definir uma tarefa de arranque que configura [serviços de informação Internet (IIS)] [ iis.net] para utilizar o runtime PHP para processar pedidos para `.php` páginas. Para tal, abra o `setup_web.cmd` ficheiro (no `bin` ficheiros do diretório de raiz da sua função da web) num editor de texto e substitua o conteúdo com o seguinte script:

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
5. Adicione os ficheiros da aplicação para o diretório de raiz da sua função da web. Este será o diretório de raiz do servidor web.
6. Publicar a sua aplicação, conforme descrito no [publicar a sua aplicação](#publish-your-application) secção abaixo.

> [!NOTE]
> O `download.ps1` script (no `bin` pasta do diretório de raiz de função da web) pode ser eliminado depois de seguir os passos descritos acima para utilizar o seu próprio tempo de execução do PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para utilizar o seu próprio tempo de execução do PHP

Para configurar uma função de trabalho para utilizar um runtime PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicionar uma função de trabalho do PHP, conforme descrito anteriormente neste tópico.
2. Criar uma `php` pasta no diretório de raiz a função de trabalho e, em seguida, adicione o seu tempo de execução do PHP (todos os binários, arquivos de configuração, as subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se utilizar o seu tempo de execução do PHP [Drivers Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a sua função de trabalho para instalar [SQL Server Native Client 2012] [ sql native client] quando está aprovisionado. Para tal, adicione a [instalador de sqlncli.msi x64] para diretório de raiz a função de trabalho. O script de inicialização descrito no próximo passo será silenciosamente execute o instalador, quando a função está aprovisionada. Se o runtime PHP não utiliza os Drivers Microsoft para PHP para SQL Server, pode remover a seguinte linha do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definir uma tarefa de arranque que adiciona o `php.exe` executável à variável de ambiente para caminho da função de trabalho quando a função está aprovisionada. Para tal, abra o `setup_worker.cmd` (no diretório de raiz a função de trabalho) de ficheiros num editor de texto e substituir seu conteúdo com o seguinte script:

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
5. Adicione os ficheiros da aplicação para o diretório de raiz de sua função de trabalho.
6. Publicar a sua aplicação, conforme descrito no [publicar a sua aplicação](#publish-your-application) secção abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Execute a sua aplicação nos emuladores de computação e armazenamento

Os emuladores do Azure fornecem um ambiente local no qual pode testar a aplicação do Azure antes de implementá-la para a cloud. Existem algumas diferenças entre os emuladores e o ambiente do Azure. Para entender isso melhor, veja [utilizar o emulador de armazenamento do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).

Tenha em atenção que tem de ter o PHP instalado localmente para utilizar o emulador de computação. O emulador de computação irá utilizar a instalação do PHP local para executar a sua aplicação.

Para executar o seu projeto nos emuladores, execute o seguinte comando a partir do diretório de raiz do seu projeto:

    PS C:\MyProject> Start-AzureEmulator

Verá a saída semelhante a este:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Pode ver a sua aplicação em execução no emulador ao abrir um navegador da web e navegar para o endereço local apresentado no resultado (`http://127.0.0.1:81` no resultado de exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar a aplicação

Para publicar a sua aplicação, tem de importar primeiro suas definições de publicação utilizando o [importação AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) cmdlet. Em seguida, publicar a aplicação utilizando o [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) cmdlet. Para obter informações sobre a iniciar sessão, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte a [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição (. csdef) do serviço]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração de serviço (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalador de sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
