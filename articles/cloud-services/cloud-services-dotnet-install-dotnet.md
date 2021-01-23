---
title: Instale .NET em funções Azure Cloud Services (clássico) | Microsoft Docs
description: Este artigo descreve como instalar manualmente o Quadro .NET na sua web de serviço na nuvem e funções de trabalhador
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 18665fabad079a8759f26be8834b2fe029ab5f49
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742782"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Instale .NET em funções Azure Cloud Services (clássicos)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo descreve como instalar versões de .NET Framework que não vêm com o Azure Guest OS. Pode utilizar .NET no Guest OS para configurar as suas funções web e de trabalho do seu serviço de nuvem.

Por exemplo, pode instalar o Quadro .NET 4.6.2 na família Guest OS 4, que não vem com qualquer lançamento de .NET Framework 4.6. (A família Guest OS 5 vem com .NET Framework 4.6.) Para obter as informações mais recentes sobre os lançamentos do Azure Guest OS, consulte as notícias de lançamento do [Azure Guest OS](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>O Azure SDK 2.9 contém uma restrição na implementação do Quadro 4.NET 4.6 na família Guest OS 4 ou anteriormente. Uma correção para a restrição está disponível no site do [Microsoft Docs.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Para instalar .NET nas suas funções web e trabalhadora, inclua o instalador web .NET como parte do seu projeto de serviço na nuvem. Inicie o instalador como parte das tarefas de arranque da função. 

## <a name="add-the-net-installer-to-your-project"></a>Adicione o instalador .NET ao seu projeto
Para descarregar o instalador web para o Quadro .NET, escolha a versão que pretende instalar:

* [.NET Framework 4.8 instalador web](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET Quadro 4.7.2 instalador web](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Quadro 4.6.2 instalador web](https://www.microsoft.com/download/details.aspx?id=53345)

Para adicionar o instalador para uma função *web:*
  1. No **Solution Explorer**, em **Funções** no seu projeto de serviço na nuvem, clique com o botão direito no seu papel *web* e selecione **Adicionar**  >  **Nova Pasta**. Criar uma pasta chamada **bin**.
  2. Clique com o botão direito na pasta do caixote do lixo e **selecione Adicionar** o  >  **Item Existente**. Selecione o instalador .NET e adicione-o à pasta do caixote do lixo.
  
Para adicionar o instalador para um papel *de trabalhador:*
* Clique com o botão direito na sua função *de trabalhador* e selecione **Adicionar** O  >  **Item Existente**. Selecione o instalador .NET e adicione-o à função. 

Quando os ficheiros são adicionados desta forma à pasta de conteúdo de função, são automaticamente adicionados ao seu pacote de serviços na nuvem. Os ficheiros são então implantados para uma localização consistente na máquina virtual. Repita este processo para cada web e papel de trabalhador no seu serviço de nuvem para que todas as funções tenham uma cópia do instalador.

> [!NOTE]
> Deverá instalar o Quadro .NET 4.6.2 na sua função de serviço de cloud, mesmo que a sua aplicação tenha como alvos .NET Framework 4.6. O Guest OS inclui a [atualização 3098779](https://support.microsoft.com/kb/3098779) da Base de Conhecimento e [a atualização 3097997](https://support.microsoft.com/kb/3097997). Os problemas podem ocorrer quando executar as suas aplicações .NET se o Quadro 4.6 .NET estiver instalado no topo das atualizações da Base de Conhecimento. Para evitar estes problemas, instale o Quadro .NET 4.6.2 em vez da versão 4.6. Para mais informações, consulte a Base de [Conhecimento artigo 3118750](https://support.microsoft.com/kb/3118750) e [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Funções de conteúdo com ficheiros instaladores][1]

## <a name="define-startup-tasks-for-your-roles"></a>Defina tarefas de arranque para as suas funções
Pode utilizar tarefas de arranque para executar operações antes de começar uma função. A instalação do Quadro .NET como parte da tarefa de arranque garante que a estrutura é instalada antes de qualquer código de aplicação ser executado. Para obter mais informações sobre as tarefas de arranque, consulte [executar tarefas de arranque no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte conteúdo ao ficheiro ServiceDefinition.csdef no nó **WebRole** ou **WorkerRole** para todas as funções:
   
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
   
    A configuração anterior executa o comando da consola `install.cmd` com privilégios de administrador para instalar o Quadro .NET. A configuração também cria um elemento **local de armazenamento** chamado **NETFXInstall**. O script de arranque define a pasta temporária para usar este recurso de armazenamento local. 
    
    > [!IMPORTANT]
    > Para garantir uma correta instalação da estrutura, desagure o tamanho deste recurso em pelo menos 1.024 MB.
    
    Para obter mais informações sobre as tarefas de arranque, consulte [as tarefas de arranque da Common Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Crie um ficheiro chamado **install.cmd** e adicione o seguinte script de instalação ao ficheiro.

   O script verifica se a versão especificada do Quadro .NET já está instalada na máquina consultando o registo. Se a versão .NET Framework não for instalada, o instalador web .NET Framework é aberto. Para ajudar a resolver problemas, o script regista toda a atividade no startup de ficheiros startuptasklog-(data e hora corrente).txt que é armazenado no armazenamento local **de InstallLogs.**
   
   > [!IMPORTANT]
   > Utilize um editor de texto básico como o Windows Notepad para criar o ficheiro install.cmd. Se utilizar o Visual Studio para criar um ficheiro de texto e alterar a extensão para .cmd, o ficheiro poderá ainda conter uma marca de ordem de byte UTF-8. Esta marca pode causar um erro quando a primeira linha do script é executada. Para evitar este erro, faça da primeira linha do script uma declaração REM que pode ser ignorada pelo processamento da encomenda byte. 
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

3. Adicione o ficheiro install.cmd a cada função utilizando **adicionar**  >  **o item existente** no Solution **Explorer,** conforme descrito anteriormente neste tópico. 

    Após a conclusão deste passo, todas as funções devem ter o ficheiro instalador .NET e o ficheiro instalar.cmd.

   ![Conteúdo de função com todos os ficheiros][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configurar diagnósticos para transferir registos de startups para o armazenamento blob
Para simplificar problemas de instalação de resolução de problemas, pode configurar o Azure Diagnostics para transferir quaisquer ficheiros de registo gerados pelo script de arranque ou pelo instalador .NET para o armazenamento do Azure Blob. Ao utilizar esta abordagem, pode visualizar os registos descarregando os ficheiros de registo do armazenamento blob em vez de ter de fazer desktop remoto para a função.


Para configurar os Diagnósticos, abra o ficheiro diagnostics.wadcfgx e adicione o seguinte conteúdo no nó **de Diretórios:** 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Este XML configura o Diagnóstico para transferir os ficheiros do diretório de registos no recurso **NETFXInstall** para a conta de armazenamento de Diagnósticos no recipiente blob instalado pela **Netfx.**

## <a name="deploy-your-cloud-service"></a>Implemente o seu serviço na nuvem
Quando implementa o seu serviço de nuvem, as tarefas de arranque instalam o Quadro .NET se ainda não estiver instalado. As suas funções de serviço na nuvem estão no estado *ocupado* enquanto a estrutura está a ser instalada. Se a instalação-quadro necessitar de um reinício, as funções de serviço também podem reiniciar. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalação do Quadro .NET][Installing the .NET Framework]
* [Determinar quais as versões .NET Framework instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Resolução de problemas .NET Instalações-quadro][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



