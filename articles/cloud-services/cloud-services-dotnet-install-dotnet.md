---
title: Instalar .NET nas funções Azure Cloud Services [ Serviços De Nuvem Azul] Microsoft Docs
description: Este artigo descreve como instalar manualmente o .NET Framework na sua web de serviço na nuvem e funções de trabalhador
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214712"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalar .NET nas funções azure cloud services
Este artigo descreve como instalar versões de .NET Framework que não vêm com o Os De hóspedes azure. Pode utilizar .NET no Os convidado para configurar as funções web e de trabalhadores do serviço na nuvem.

Por exemplo, pode instalar .NET Framework 4.6.2 na família Os 4 do Guest OS, que não vem com qualquer lançamento de .NET Framework 4.6. (A família Os 5 hóspede seleção vem com .NET Framework 4.6.) Para obter as últimas informações sobre os lançamentos do Os O, consulte as novidades de lançamento do [Azure Guest OS](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>O Azure SDK 2.9 contém uma restrição à implantação .NET Framework 4.6 na família Os 4 ou anterior. Uma correção para a restrição está disponível no site do [Microsoft Docs.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Para instalar .NET nas suas funções web e trabalhador, inclua o instalador web .NET como parte do seu projeto de serviço na nuvem. Inicie o instalador como parte das tarefas de arranque do papel. 

## <a name="add-the-net-installer-to-your-project"></a>Adicione o instalador .NET ao seu projeto
Para descarregar o instalador web para o .NET Framework, escolha a versão que pretende instalar:

* [.NET Quadro 4.8 instalador web](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET Quadro 4.7.2 instalador web](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Quadro 4.6.2 instalador web](https://www.microsoft.com/download/details.aspx?id=53345)

Para adicionar o instalador para uma função *web:*
  1. No **Solution Explorer**, em **funções** no seu projeto de serviço na nuvem, clique na sua função *web* e selecione **Adicionar** > **Nova Pasta**. Crie uma pasta chamada **bin**.
  2. Clique na pasta do lixo e selecione **Adicionar** > **item existente**. Selecione o instalador .NET e adicione-o à pasta do lixo.
  
Para adicionar o instalador para uma função *de trabalhador:*
* Clique à direita na sua função de *trabalhador* e selecione **Adicionar** > **Item Existente**. Selecione o instalador .NET e adicione-o à função. 

Quando os ficheiros são adicionados desta forma à pasta de conteúdo sonoro, são automaticamente adicionados ao seu pacote de serviço na nuvem. Os ficheiros são então implantados para uma localização consistente na máquina virtual. Repita este processo para cada função web e trabalhador no seu serviço na nuvem para que todas as funções tenham uma cópia do instalador.

> [!NOTE]
> Deve instalar .NET Framework 4.6.2 na sua função de serviço na nuvem, mesmo que os seus alvos de aplicação .NET Framework 4.6. O Os Convidado inclui a atualização da Base de Conhecimento [3098779](https://support.microsoft.com/kb/3098779) e [a atualização 3097997](https://support.microsoft.com/kb/3097997). Os problemas podem ocorrer quando executa as suas aplicações .NET se a .NET Framework 4.6 estiver instalada em cima das atualizações da Base de Conhecimento. Para evitar estes problemas, instale .NET Framework 4.6.2 em vez da versão 4.6. Para mais informações, consulte o [artigo 3118750](https://support.microsoft.com/kb/3118750) e [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Conteúdo de função com ficheiros instaladores][1]

## <a name="define-startup-tasks-for-your-roles"></a>Defina tarefas de arranque para as suas funções
Pode utilizar tarefas de arranque para realizar operações antes de iniciar uma função. A instalação do .NET Framework como parte da tarefa de arranque garante que a estrutura seja instalada antes de qualquer código de aplicação ser executado. Para obter mais informações sobre as tarefas de arranque, consulte executar tarefas de [arranque em Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte conteúdo ao ficheiro ServiceDefinition.csdef sob o nó **WebRole** ou **WorkerRole** para todas as funções:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    A configuração anterior executa o comando da consola `install.cmd` com privilégios de administrador para instalar o .NET Framework. A configuração também cria um elemento **LocalStorage** chamado **NETFXInstall**. O script de arranque define a pasta temporária para utilizar este recurso de armazenamento local. 
    
    > [!IMPORTANT]
    > Para garantir a correta instalação da estrutura, detete a dimensão deste recurso para pelo menos 1.024 MB.
    
    Para obter mais informações sobre as tarefas de arranque, consulte as tarefas de [arranque da Common Azure Cloud Services.](cloud-services-startup-tasks-common.md)

2. Crie um ficheiro chamado **install.cmd** e adicione o seguinte script de instalação ao ficheiro.

   O script verifica se a versão especificada do .NET Framework já está instalada na máquina consultando o registo. Se a versão .NET Framework não for instalada, então o instalador web .NET Framework é aberto. Para ajudar a resolver problemas, o script regista toda a atividade no ficheiro startuptasklog-(data e hora atual).txt que é armazenado em **InstallLogs** armazenamento local.
   
   > [!IMPORTANT]
   > Utilize um editor de texto básico como o Windows Notepad para criar o ficheiro install.cmd. Se utilizar o Visual Studio para criar um ficheiro de texto e alterar a extensão para .cmd, o ficheiro ainda pode conter uma marca de byte UTF-8. Esta marca pode causar um erro quando a primeira linha do script é executada. Para evitar este erro, faça da primeira linha do script uma declaração REM que pode ser ignorada pelo processamento de encomendas byte. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Adicione o ficheiro install.cmd a cada função utilizando **add** > **item existente** no **Solution Explorer,** como descrito anteriormente neste tópico. 

    Depois de concluído este passo, todas as funções devem ter o ficheiro do instalador .NET e o ficheiro install.cmd.

   ![Conteúdo de função com todos os ficheiros][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configure Diagnósticos para transferir registos de startups para o armazenamento blob
Para simplificar os problemas de instalação de resolução de problemas, pode configurar o Azure Diagnostics para transferir quaisquer ficheiros de registo gerados pelo script de arranque ou pelo instalador .NET para o armazenamento Azure Blob. Ao utilizar esta abordagem, pode visualizar os registos baixando os ficheiros de registo do armazenamento Blob em vez de ter de se remeter para o trabalho.


Para configurar diagnósticos, abra o ficheiro diagnostics.wadcfgx e adicione o seguinte conteúdo sob o nó de **Diretórios:** 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Este XML confunde diagnósticos para transferir os ficheiros no diretório de registo no recurso **NETFXInstalar** para a conta de armazenamento de Diagnósticos no recipiente de blob de **instalação netfx.**

## <a name="deploy-your-cloud-service"></a>Implante o seu serviço de nuvem
Quando implementa o seu serviço na nuvem, as tarefas de arranque instalam o .NET Framework se ainda não estiver instalado. As suas funções de serviço na nuvem estão em estado *de ocupado* enquanto a estrutura está a ser instalada. Se a instalação-quadro necessitar de um reinício, as funções de serviço também poderão reiniciar. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalação do .NET Framework][Installing the .NET Framework]
* [Determinar quais as versões .NET Framework instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Resolução de problemas .NET Instalações de enquadramento][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



