---
title: Instalar o .NET em funções dos serviços de nuvem do Azure | Microsoft Docs
description: Este artigo descreve como instalar manualmente o .NET Framework em suas funções Web e de trabalho do serviço de nuvem
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c950fbedde19e3b7708d3640487d413fcac7787f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360995"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalar o .NET em funções dos serviços de nuvem do Azure
Este artigo descreve como instalar versões do .NET Framework que não vêm com o sistema operacional convidado do Azure. Você pode usar o .NET no sistema operacional convidado para configurar suas funções Web e de trabalho do serviço de nuvem.

Por exemplo, você pode instalar o .NET 4.6.2 na família de sistemas operacionais convidados 4, que não vem com nenhuma versão do .NET 4,6. (A família de sistemas operacionais convidados 5 vem com o .NET 4,6.) Para obter as informações mais recentes sobre as versões do sistema operacional convidado do Azure, consulte [notícias de versão do SO convidado do Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>O SDK 2,9 do Azure contém uma restrição sobre a implantação do .NET 4,6 na família de sistemas operacionais convidados 4 ou anterior. Uma correção para a restrição está disponível no site do [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) .

Para instalar o .NET em suas funções Web e de trabalho, inclua o instalador do .NET Web como parte do seu projeto de serviço de nuvem. Inicie o instalador como parte das tarefas de inicialização da função. 

## <a name="add-the-net-installer-to-your-project"></a>Adicionar o instalador do .NET ao seu projeto
Para baixar o instalador da Web para o .NET Framework, escolha a versão que você deseja instalar:

* [Instalador da Web do .NET 4,8](https://dotnet.microsoft.com/download/thank-you/net48)
* [Instalador da Web do .NET 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalador da Web do .NET 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Para adicionar o instalador para uma função *Web* :
  1. Em **Gerenciador de soluções**, em **funções** em seu projeto de serviço de nuvem, clique com o botão direito do mouse em sua função *Web* e selecione **Adicionar** > **nova pasta**. Crie uma pasta chamada **bin**.
  2. Clique com o botão direito do mouse na pasta bin e selecione **adicionar** > **Item existente**. Selecione o instalador do .NET e adicione-o à pasta bin.
  
Para adicionar o instalador para uma função de *trabalho* :
* Clique com o botão direito do mouse em sua função de *trabalho* e selecione **Adicionar** > **Item existente**. Selecione o instalador do .NET e adicione-o à função. 

Quando os arquivos são adicionados dessa maneira à pasta de conteúdo da função, eles são automaticamente adicionados ao seu pacote de serviço de nuvem. Os arquivos são então implantados em um local consistente na máquina virtual. Repita esse processo para cada função Web e de trabalho em seu serviço de nuvem para que todas as funções tenham uma cópia do instalador.

> [!NOTE]
> Você deve instalar o .NET 4.6.2 em sua função de serviço de nuvem mesmo que seu aplicativo tenha como destino o .NET 4,6. O sistema operacional convidado inclui a [atualização 3098779](https://support.microsoft.com/kb/3098779) da base de dados de conhecimento e a [atualização 3097997](https://support.microsoft.com/kb/3097997). Poderão ocorrer problemas quando você executar seus aplicativos .NET se o .NET 4,6 estiver instalado sobre as atualizações da base de dados de conhecimento. Para evitar esses problemas, instale o .NET 4.6.2 em vez da versão 4,6. Para obter mais informações, consulte o [artigo 3118750 da base de dados de conhecimento](https://support.microsoft.com/kb/3118750) e [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Conteúdo da função com arquivos do instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir tarefas de inicialização para suas funções
Você pode usar tarefas de inicialização para executar operações antes de uma função ser iniciada. Instalar o .NET Framework como parte da tarefa de inicialização garante que a estrutura seja instalada antes de qualquer código de aplicativo ser executado. Para obter mais informações sobre tarefas de inicialização, consulte [executar tarefas de inicialização no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte conteúdo ao arquivo de @ Definition. csdef sob o nó **WebRole** ou **WorkerRole** para todas as funções:
   
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
   
    A configuração anterior executa o comando do console `install.cmd` com privilégios de administrador para instalar o .NET Framework. A configuração também cria um elemento **localStorage** chamado **NETFXInstall**. O script de inicialização define a pasta Temp para usar esse recurso de armazenamento local. 
    
    > [!IMPORTANT]
    > Para garantir a instalação correta da estrutura, defina o tamanho desse recurso como pelo menos 1.024 MB.
    
    Para obter mais informações sobre tarefas de inicialização, consulte [tarefas comuns de inicialização dos serviços de nuvem do Azure](cloud-services-startup-tasks-common.md).

2. Crie um arquivo chamado **install. cmd** e adicione o seguinte script de instalação ao arquivo.

   O script verifica se a versão especificada do .NET Framework já está instalada no computador consultando o registro. Se a versão do .NET não estiver instalada, o instalador da Web do .NET será aberto. Para ajudar a solucionar problemas, o script registra todas as atividades no arquivo startuptasklog-(data e hora atual). txt que é armazenado no armazenamento local **InstallLogs** .
   
   > [!IMPORTANT]
   > Use um editor de texto básico como o bloco de notas do Windows para criar o arquivo install. cmd. Se você usar o Visual Studio para criar um arquivo de texto e alterar a extensão para. cmd, o arquivo ainda poderá conter uma marca de ordem de byte UTF-8. Essa marca pode causar um erro quando a primeira linha do script é executada. Para evitar esse erro, torne a primeira linha do script uma instrução REM que pode ser ignorada pelo processamento da ordem de byte. 
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

3. Adicione o arquivo install. cmd a cada função usando **adicionar** > **Item existente** no **Gerenciador de soluções** conforme descrito anteriormente neste tópico. 

    Depois que essa etapa for concluída, todas as funções deverão ter o arquivo do instalador do .NET e o arquivo install. cmd.

   ![Conteúdo da função com todos os arquivos][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configurar o diagnóstico para transferir os logs de inicialização para o armazenamento de BLOBs
Para simplificar a solução de problemas de instalação, você pode configurar Diagnóstico do Azure para transferir quaisquer arquivos de log gerados pelo script de inicialização ou o instalador do .NET para o armazenamento de BLOBs do Azure. Ao usar essa abordagem, você pode exibir os logs baixando os arquivos de log do armazenamento de BLOBs em vez de ter a área de trabalho remota na função.


Para configurar o diagnóstico, abra o arquivo Diagnostics. wadcfgx e adicione o seguinte conteúdo sob o nó **diretórios** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Esse XML configura o diagnóstico para transferir os arquivos no diretório de log no recurso **NETFXInstall** para a conta de armazenamento de diagnóstico no contêiner de blob **Netfx-install** .

## <a name="deploy-your-cloud-service"></a>Implantar seu serviço de nuvem
Quando você implantar seu serviço de nuvem, as tarefas de inicialização instalarão o .NET Framework se ele ainda não estiver instalado. Suas funções de serviço de nuvem estão no estado *ocupado* enquanto a estrutura está sendo instalada. Se a instalação do Framework exigir uma reinicialização, as funções de serviço também poderão ser reiniciadas. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalando o .NET Framework][Installing the .NET Framework]
* [Determinar quais versões do .NET Framework estão instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Solução de problemas .NET Framework instalações][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



