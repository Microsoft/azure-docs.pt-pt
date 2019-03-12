---
title: Instale o .NET em funções de serviços Cloud do Azure | Documentos da Microsoft
description: Este artigo descreve como instalar manualmente o .NET Framework em suas funções de web e de trabalho de serviço do cloud
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: jeconnoc
ms.openlocfilehash: 4b6aeb2968d7642881535753cb6c8434cfe10886
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534076"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instale o .NET em funções de serviços Cloud do Azure
Este artigo descreve como instalar versões do .NET Framework que não são fornecidos com o SO convidado do Azure. Pode utilizar o .NET no SO convidado para configurar as suas funções de web e de trabalho de serviço do cloud.

Por exemplo, pode instalar o .NET 4.6.2 sobre a família de SO convidado 4, que não são fornecidos com qualquer versão do .NET 4.6. (A família de SO convidado 5 vêm com o .NET 4.6). Para as últimas informações sobre as versões de SO convidado do Azure, consulte a [notícias de lançamento do SO convidado do Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>O Azure SDK 2.9 contém uma restrição sobre como implementar o .NET 4.6 a família de SO convidado 4 ou anterior. Uma correção para a restrição está disponível na [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) site.

Para instalar o .NET em suas funções da web e de trabalho, inclua o instalador da web de .NET como parte do seu projeto de serviço em nuvem. Inicie o instalador como parte das tarefas de arranque da função. 

## <a name="add-the-net-installer-to-your-project"></a>Adicionar o programa de instalação do .NET ao seu projeto
Para transferir o instalador da web para o .NET Framework, escolha a versão que pretende instalar:

* [Instalador da web .NET 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalador da web .NET 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Para adicionar o instalador para uma *web* função:
  1. Na **Explorador de soluções**, em **funções** no seu projeto de serviço em nuvem, clique no seu *web* função e selecione **adicionar**  >  **Nova pasta**. Crie uma pasta denominada **bin**.
  2. A pasta bin com o botão direito e selecione **Add** > **Item existente**. Selecione o instalador do .NET e adicione-o para a pasta bin.
  
Para adicionar o instalador para uma *trabalho* função:
* Com o botão direito sua *worker* função e selecione **Add** > **Item existente**. Selecione o instalador do .NET e adicione-à função. 

Quando forem adicionados ficheiros dessa maneira para a pasta de conteúdo de função, eles serão adicionados automaticamente ao seu pacote de serviço cloud. Os ficheiros, em seguida, são implementados numa localização consistente na máquina virtual. Repita este processo para cada função da web e de trabalho no seu serviço cloud, para que todas as funções de tem uma cópia do programa de instalação.

> [!NOTE]
> Deve instalar o .NET 4.6.2 na sua função de serviço cloud, mesmo se a sua aplicação .NET 4.6. O SO convidado inclui a Base de dados de conhecimento [atualizar 3098779](https://support.microsoft.com/kb/3098779) e [atualizar 3097997](https://support.microsoft.com/kb/3097997). Podem ocorrer problemas quando executa as suas aplicações de .NET se o .NET 4.6 é instalado sobre as atualizações de Base de dados de conhecimento. Para evitar estes problemas, instale o .NET 4.6.2 em vez de versão 4.6. Para obter mais informações, consulte a [artigo da Base de dados de conhecimento 3118750](https://support.microsoft.com/kb/3118750) e [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Conteúdo de função com arquivos do instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir as tarefas de arranque para as funções
Pode utilizar tarefas de arranque para executar operações antes de uma função de inicialização. Instalar o .NET Framework como parte da tarefa de arranque garante que o framework está instalado antes de qualquer código de aplicação é executado. Para obter mais informações sobre tarefas de arranque, consulte [executar tarefas de arranque no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte conteúdo para o ficheiro servicedefinition. Csdef com o **WebRole** ou **WorkerRole** nó para todas as funções:
   
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
   
    A configuração anterior executa o comando de consola `install.cmd` com privilégios de administrador para instalar o .NET Framework. A configuração também cria um **LocalStorage** com o nome de elemento **NETFXInstall**. O script de inicialização define a pasta temporária para utilizar este recurso de armazenamento local. 
    
    > [!IMPORTANT]
    > Para garantir uma instalação correta do framework, defina o tamanho deste recurso para, pelo menos, 1.024 MB.
    
    Para obter mais informações sobre tarefas de arranque, consulte [tarefas de arranque de serviços Cloud do Azure comuns](cloud-services-startup-tasks-common.md).

2. Crie um ficheiro denominado **install.cmd** e adicione o seguinte script de instalação para o ficheiro.

   O script verifica se a versão especificada do .NET Framework já está instalada na máquina, consultando o registo. Se a versão do .NET não estiver instalada, o instalador do .NET web é aberto. Para ajudar a resolver quaisquer problemas, os registos dos scripts toda a atividade para o ficheiro startuptasklog-(data e hora atuais). txt que esteja armazenado em **InstallLogs** armazenamento local.
   
   > [!IMPORTANT]
   > Utilize um editor de texto básica como o bloco de notas do Windows para criar o ficheiro de install.cmd. Se utilizar o Visual Studio para criar um arquivo de texto e altere a extensão para. cmd, o ficheiro ainda poderá conter uma marca de ordem de byte de UTF-8. Essa marca pode causar um erro quando a primeira linha do script é executada. Para evitar este erro, verifique, por exemplo, uma instrução de REM que pode ser ignorada pelo processamento da ordem de byte a primeira linha do script. 
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

3. Adicionar o ficheiro de install.cmd a cada função utilizando **Add** > **Item existente** no **Explorador de soluções** conforme descrito anteriormente neste tópico. 

    Após concluir este passo, todas as funções devem ter o ficheiro de instalador do .NET e o ficheiro de install.cmd.

   ![Conteúdo de função com todos os ficheiros][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configurar diagnósticos para transferir os registos de arranque para o armazenamento de BLOBs
Para simplificar a resolução de problemas de instalação, pode configurar o diagnóstico do Azure para transferir quaisquer ficheiros de registo gerados pelo script de inicialização ou o instalador do .NET para o armazenamento de Blobs do Azure. Ao utilizar esta abordagem, pode ver os registos ao transferir os ficheiros de registo do armazenamento de BLOBs, em vez de ter para o ambiente de trabalho remoto para a função.


Para configurar diagnósticos, abra o ficheiro de diagnostics.wadcfgx e adicione o seguinte conteúdo sob o **diretórios** nó: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Esse XML configura diagnósticos para transferir os ficheiros no diretório de registo no **NETFXInstall** recurso para a conta de armazenamento de diagnósticos no **netfx-install** contentor de Blobs.

## <a name="deploy-your-cloud-service"></a>Implementar o seu serviço cloud
Ao implementar o seu serviço cloud, as tarefas de arranque instalar o .NET Framework, se ainda não estiver instalado. As funções de serviço em nuvem estão a *ocupado* enquanto está a ser instalado o framework de estado. Se a instalação do framework exigir um reinício, as funções de serviço também poderão reiniciar. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalar o .NET Framework][Installing the .NET Framework]
* [Determinar quais versões do .NET Framework estão instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Resolução de problemas de instalações do .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
