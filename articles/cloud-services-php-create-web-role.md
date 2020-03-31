---
title: Criar funções web e trabalhadores azure para PHP
description: Um guia para criar funções php web e trabalhador num serviço de nuvem Azure, e configurar o tempo de execução PHP.
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
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297259"
---
# <a name="create-php-web-and-worker-roles"></a>Criar funções de Web PHP e de trabalho

## <a name="overview"></a>Descrição geral

Este guia irá mostrar-lhe como criar funções php web ou trabalhador num ambiente de desenvolvimento do Windows, escolher uma versão específica de PHP a partir das versões "incorporadas" disponíveis, alterar a configuração PHP, ativar extensões e, finalmente, implementar para o Azure. Também descreve como configurar uma função web ou trabalhadora para usar um tempo de execução PHP (com configuração e extensões personalizadas) que fornece.

O Azure fornece três modelos de cálculo para aplicações em execução: Serviço de Aplicações Azure, Máquinas Virtuais Azure e Serviços Azure Cloud. Todos os três modelos suportam PHP. A Cloud Services, que inclui funções web e operárias, fornece *a plataforma como um serviço (PaaS)*. Dentro de um serviço na nuvem, uma função web fornece um servidor web dedicado dos Serviços de Informação de Internet (IIS) para hospedar aplicações web front-end. Um papel do trabalhador pode executar tarefas assíncronas, de longa duração ou perpétuas independentes da interação ou entrada do utilizador.

Para obter mais informações sobre estas opções, consulte as opções de [hospedagem computacionais fornecidas pelo Azure.](cloud-services/cloud-services-choose-me.md)

## <a name="download-the-azure-sdk-for-php"></a>Descarregue o Azure SDK para PHP

O [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) é composto por vários componentes. Este artigo utilizará dois deles: o Azure PowerShell e os emuladores Azure. Estes dois componentes podem ser instalados através do Instalador da Plataforma Web da Microsoft. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de Serviços cloud

O primeiro passo para criar uma função php web ou trabalhador é criar um projeto de Serviço Azure. um projeto do Serviço Azure serve como um recipiente lógico para funções web e trabalhador, e contém a definição de serviço do projeto [(.csdef)] e os ficheiros de configuração de [serviço (.cscfg).]

Para criar um novo projeto do Serviço Azure, execute a Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Este comando criará um`myProject`novo diretório ao qual pode adicionar funções web e trabalhadores.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções php web ou trabalhador

Para adicionar uma função web php a um projeto, executar o seguinte comando a partir do diretório raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para um papel de trabalhador, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> O `roleName` parâmetro é opcional. Se for omitida, o nome do papel será automaticamente gerado. O primeiro papel web `WebRole1`criado será `WebRole2`, o segundo será , e assim por diante. O primeiro papel de `WorkerRole1`trabalhador criado `WorkerRole2`será , o segundo será , e assim por diante.
>
>

## <a name="use-your-own-php-runtime"></a>Use o seu próprio tempo de execução PHP

Em alguns casos, em vez de selecionar um tempo de funcionamento php incorporado e configurá-lo como descrito acima, você pode querer fornecer o seu próprio tempo de execução PHP. Por exemplo, pode utilizar o mesmo tempo de execução php numa função web ou trabalhadora que utiliza no seu ambiente de desenvolvimento. Isto facilita a garantia de que a aplicação não mudará o comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configure uma função web para usar o seu próprio tempo de execução PHP

Para configurar uma função web para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Crie um projeto de Serviço Azure e adicione uma função web PHP como descrito anteriormente neste tópico.
2. Crie `php` uma `bin` pasta na pasta que esteja no diretório raiz do seu papel web e, em seguida, adicione o seu `php` tempo de execução PHP (todos os binários, ficheiros de configuração, subpastas, etc.) à pasta.
3. (OPCIONAL) Se o seu tempo de execução PHP utilizar os [Controladores microsoft para PHP para O Servidor SQL,][sqlsrv drivers]terá de configurar a sua função web para instalar o [SQL Server Native Client 2012][sql native client] quando este estiver provisionado. Para tal, adicione o [instalador sqlncli.msi x64] à `bin` pasta no diretório raiz do seu papel web. O script de arranque descrito no próximo passo executará silenciosamente o instalador quando a função for aprovisionada. Se o seu tempo de execução PHP não utilizar os Controladores da Microsoft para PHP para O Servidor SQL, pode remover a seguinte linha do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa de arranque que configura os Serviços de Informação da `.php` [Internet (IIS)][iis.net] para utilizar o seu tempo de execução PHP para lidar com pedidos de páginas. Para tal, abra `setup_web.cmd` o ficheiro `bin` (no ficheiro do diretório raiz do seu papel web) num editor de texto e substitua os seus conteúdos pelo seguinte script:

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
5. Adicione os seus ficheiros de aplicação ao diretório raiz do seu papel web. Este será o diretório raiz do servidor web.
6. Publique a sua aplicação conforme descrito na secção [publicar](#publish-your-application) abaixo.

> [!NOTE]
> O `download.ps1` script (na pasta do diretório raiz do papel web) pode ser eliminado depois de seguir os passos acima descritos para usar o `bin` seu próprio tempo de execução PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configure uma função de trabalhador para usar o seu próprio tempo de execução PHP

Para configurar uma função de trabalhador para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Crie um projeto de Serviço Azure e adicione um papel de trabalhador PHP como descrito anteriormente neste tópico.
2. Crie `php` uma pasta no diretório raiz do papel do trabalhador e, em seguida, adicione o seu tempo `php` de execução PHP (todos os binários, ficheiros de configuração, subpastas, etc.) à pasta.
3. (OPCIONAL) Se o seu tempo de execução PHP utilizar [os Controladores Microsoft para PHP para o SQL Server,][sqlsrv drivers]terá de configurar a sua função de trabalhador para instalar o [SQL Server Native Client 2012][sql native client] quando este estiver provisionado. Para tal, adicione o [instalador sqlncli.msi x64] ao diretório raiz do trabalhador. O script de arranque descrito no próximo passo executará silenciosamente o instalador quando a função for aprovisionada. Se o seu tempo de execução PHP não utilizar os Controladores da Microsoft para PHP para O Servidor SQL, pode remover a seguinte linha do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa `php.exe` de arranque que adiciona a sua executável à variável ambiental PATH do papel do trabalhador quando a função é aprovisionada. Para tal, abra `setup_worker.cmd` o ficheiro (no diretório raiz do trabalhador) num editor de texto e substitua os seus conteúdos pelo seguinte guião:

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
5. Adicione os seus ficheiros de candidatura ao diretório raiz do seu papel de trabalhador.
6. Publique a sua aplicação conforme descrito na secção [publicar](#publish-your-application) abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Execute a sua aplicação nos emuladores de cálculo e armazenamento

Os emuladores Azure proporcionam um ambiente local no qual pode testar a sua aplicação Azure antes de a colocar na nuvem. Existem algumas diferenças entre os emuladores e o ambiente Azure. Para entender melhor isto, consulte [Utilize o emulador de armazenamento Azure para desenvolvimento e teste.](storage/common/storage-use-emulator.md)

Note que deve ter PHP instalado localmente para utilizar o emulador computacional. O emulador computacional utilizará a sua instalação PHP local para executar a sua aplicação.

Para executar o seu projeto nos emuladores, execute o seguinte comando a partir do diretório raiz do seu projeto:

    PS C:\MyProject> Start-AzureEmulator

Verá uma saída semelhante a esta:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Pode ver a sua aplicação a funcionar no emulador abrindo um navegador web`http://127.0.0.1:81` e navegando para o endereço local mostrado na saída (na saída de exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar a aplicação

Para publicar a sua aplicação, é necessário importar primeiro as definições de publicação utilizando o cmdlet [Import-AzurePublishSettingsFile.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Em seguida, pode publicar a sua aplicação utilizando o cmdlet [Publish-AzureServiceProject.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Para obter informações sobre o início, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte o [PhP Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição de serviço (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração do serviço (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalador sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
