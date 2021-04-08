---
title: Criar funções web e trabalhadoras da Azure para PHP
description: Um guia para criar funções web e trabalhadoras PHP num serviço de nuvem Azure e configurar o tempo de execução php.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95519851"
---
# <a name="create-php-web-and-worker-roles"></a>Criar funções de Web PHP e de trabalho

## <a name="overview"></a>Descrição Geral

Este guia irá mostrar-lhe como criar funções de web PHP ou trabalhadores num ambiente de desenvolvimento do Windows, escolher uma versão específica de PHP a partir das versões "incorporadas" disponíveis, alterar a configuração PHP, ativar extensões e, finalmente, implementar para Azure. Também descreve como configurar uma função web ou de trabalhador para usar um tempo de execução PHP (com configuração e extensões personalizadas) que fornece.

O Azure disponibiliza três modelos computacionais para aplicações em execução: Azure App Service, Azure Virtual Machines e Azure Cloud Services. Todos os três modelos suportam PHP. A Cloud Services, que inclui funções web e de trabalhadores, fornece *plataforma como um serviço (PaaS)*. Dentro de um serviço de nuvem, uma função web fornece um servidor web dedicado dos Serviços de Informação da Internet (IIS) para hospedar aplicações web front-end. Um papel de trabalhador pode executar tarefas assíncronos, de longa duração ou perpétuas independentes da interação ou entrada do utilizador.

Para obter mais informações sobre estas opções, consulte [as opções de hospedagem do Compute fornecidas pela Azure.](cloud-services/cloud-services-choose-me.md)

## <a name="download-the-azure-sdk-for-php"></a>Descarregue o Azure SDK para PHP

O [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) é composto por vários componentes. Este artigo utilizará dois deles: Azure PowerShell e os emuladores Azure. Estes dois componentes podem ser instalados através do Instalador da Plataforma Web da Microsoft. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de Cloud Services

O primeiro passo para a criação de uma web php ou papel de trabalhador é criar um projeto de Serviço Azure. um projeto de Serviço Azure serve como um recipiente lógico para funções web e trabalhadores, e contém a definição de serviço do projeto [(.csdef)] e [os ficheiros de configuração de serviço (.cscfg).]

Para criar um novo projeto do Serviço Azure, execute a Azure PowerShell como administrador e execute o seguinte comando:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Este comando criará um novo diretório `myProject` ao qual pode adicionar funções web e trabalhadoras.

## <a name="add-php-web-or-worker-roles"></a>Adicione funções web php ou trabalhador

Para adicionar uma função web PHP a um projeto, executar o seguinte comando a partir do diretório raiz do projeto:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Para um papel de trabalhador, use este comando:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> O `roleName` parâmetro é opcional. Se for omitida, o nome da função será gerado automaticamente. O primeiro papel web criado será `WebRole1` , o segundo será - e assim por `WebRole2` diante. O primeiro papel de trabalhador criado será `WorkerRole1` - o segundo será - e assim por `WorkerRole2` diante.
>
>

## <a name="use-your-own-php-runtime"></a>Use o seu próprio tempo de execução PHP

Em alguns casos, em vez de selecionar um tempo de execução php incorporado e configurar como descrito acima, pode querer fornecer o seu próprio tempo de funcionamento php. Por exemplo, pode utilizar o mesmo tempo de funcionação php numa web ou função de trabalhador que utiliza no seu ambiente de desenvolvimento. Isto facilita a garantia de que a aplicação não mudará o comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configure um papel web para usar o seu próprio tempo de execução PHP

Para configurar uma função web para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Crie um projeto de Serviço Azure e adicione um papel web PHP como descrito anteriormente neste tópico.
2. Crie uma `php` pasta na pasta que esteja no `bin` diretório de raiz da sua função web e, em seguida, adicione o seu tempo de funcionação PHP (todos os binários, ficheiros de configuração, subpagadores, etc.) à `php` pasta.
3. (OPCIONAL) Se o seu tempo de funcionamento PHP utilizar os [Microsoft Drivers for PHP para SQL Server,][sqlsrv drivers]terá de configurar a sua função web para instalar [o SQL Server Native Client 2012][sql native client] quando este for provisionado. Para isso, adicione o [ instaladorsqlncli.msi x64] à `bin` pasta no diretório de raiz da sua função web. O script de arranque descrito no passo seguinte executará silenciosamente o instalador quando a função for avisionada. Se o seu tempo de funcionação PHP não utilizar os Microsoft Drivers para PHP para SQL Server, pode remover a seguinte linha do script mostrado no passo seguinte:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Defina uma tarefa de arranque que configura [os Serviços de Informação da Internet (IIS)][iis.net] para utilizar o seu tempo de funcionação php para lidar com pedidos de `.php` páginas. Para isso, abra o `setup_web.cmd` ficheiro (no `bin` ficheiro do diretório de raiz da sua função web) num editor de texto e substitua o seu conteúdo pelo seguinte script:

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
5. Adicione os seus ficheiros de aplicação ao diretório de raiz da sua função web. Este será o diretório de raiz do servidor web.
6. Publique a sua aplicação conforme descrito na secção publicar abaixo a sua secção de [aplicação.](#publish-your-application)

> [!NOTE]
> O `download.ps1` script (na `bin` pasta do diretório de raiz da função web) pode ser eliminado depois de seguir os passos acima descritos para utilizar o seu próprio tempo de funcionação PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configure um papel de trabalhador para usar o seu próprio tempo de funcionamento PHP

Para configurar um papel de trabalhador para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Crie um projeto de Serviço Azure e adicione um papel de trabalhador PHP como descrito anteriormente neste tópico.
2. Crie uma `php` pasta no diretório de raiz do trabalho e, em seguida, adicione o seu tempo de funcionação PHP (todos os binários, ficheiros de configuração, subpaminas, etc.) à `php` pasta.
3. (OPCIONAL) Se o seu tempo de funcionamento PHP utilizar [o Microsoft Drivers para PHP para o SQL Server,][sqlsrv drivers]terá de configurar a sua função de trabalhador para instalar o [SQL Server Native Client 2012][sql native client] quando este for provisionado. Para isso, adicione o [ instaladorsqlncli.msi x64] ao diretório de raiz do papel operário. O script de arranque descrito no passo seguinte executará silenciosamente o instalador quando a função for avisionada. Se o seu tempo de funcionação PHP não utilizar os Microsoft Drivers para PHP para SQL Server, pode remover a seguinte linha do script mostrado no passo seguinte:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Defina uma tarefa de arranque que adicione o seu `php.exe` executável à variável ambiente PATH do papel do trabalhador quando o papel é aprovisionado. Para tal, abra o `setup_worker.cmd` ficheiro (no diretório principal do trabalhador) num editor de texto e substitua o seu conteúdo pelo seguinte script:

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
5. Adicione os seus ficheiros de aplicação ao diretório de raiz do seu trabalho.
6. Publique a sua aplicação conforme descrito na secção publicar abaixo a sua secção de [aplicação.](#publish-your-application)

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Execute a sua aplicação nos emuladores de computação e armazenamento

Os emuladores Azure fornecem um ambiente local no qual pode testar a sua aplicação Azure antes de a colocar na nuvem. Há algumas diferenças entre os emuladores e o ambiente Azure. Para compreender melhor isto, consulte [o Emulador de Armazenamento Azure para desenvolvimento e testes.](storage/common/storage-use-emulator.md)

Note que deve ter PHP instalado localmente para utilizar o emulador de cálculo. O emulador computatório utilizará a instalação PHP local para executar a sua aplicação.

Para executar o seu projeto nos emuladores, execute o seguinte comando a partir do diretório de raiz do seu projeto:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Verá uma saída semelhante a esta:

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Pode ver a sua aplicação a funcionar no emulador abrindo um navegador web e navegando para o endereço local mostrado na saída `http://127.0.0.1:81` (na saída de exemplo acima).

Para parar os emuladores, execute este comando:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Publicar a aplicação

Para publicar a sua aplicação, tem de importar primeiro as suas definições de publicação utilizando o [cmdlet Import-AzurePublishSettingsFile.](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) Em seguida, pode publicar a sua aplicação utilizando o [cmdlet Publish-AzureServiceProject.](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) Para obter informações sobre a sessão, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/).

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte o [Centro de Desenvolvimento PHP.](https://azure.microsoft.com/develop/php/)

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição de serviço (.csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[configuração de serviço (.cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[ instaladorsqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648