---
title: Começa com os Serviços Python e Azure Cloud (clássico)| Microsoft Docs
description: Descrição geral da utilização das Ferramentas do Python para Visual Studio para a criação de Cloud Services do Azure, incluindo funções da Web e funções de trabalho.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2822f719928515efc70eeed3d7c182e347627418
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045523"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Funções da Web e de trabalho do Python com Ferramentas de Python para Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo fornece uma descrição geral da utilização de funções da Web e de trabalho do Python através das [Ferramentas do Python para Visual Studio][Python Tools for Visual Studio]. Aprenda a utilizar o Visual Studio para criar e implementar um Serviço Cloud básico que utiliza o Python.

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2013, 2015 ou 2017](https://www.visualstudio.com/)
* [Ferramentas do Python para Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Ferramentas do Azure SDK para VS 2013][Azure SDK Tools for VS 2013] ou  
[Ferramentas do Azure SDK para VS 2015][Azure SDK Tools for VS 2015] ou  
[Ferramentas do Azure SDK para VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 32-bit][Python 2.7 32-bit] ou [Python 3.8 32-bit][Python 3.8 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>O que são as funções da Web e de trabalho do Python?
O Azure fornece três modelos de computação para a execução de aplicações: [Funcionalidade Aplicações Web no Serviço de Aplicações do Azure][execution model-web sites], [Máquinas Virtuais do Azure][execution model-vms] e [Serviços Cloud do Azure][execution model-cloud services]. Os três modelos suportam o Python. Os Cloud Services, que incluem funções da Web e de trabalho, fornecem uma *Plataforma como Serviço (PaaS)*. Dentro de um serviço cloud, uma função da Web fornece um servidor Web de Serviços de Informação Internet (IIS) dedicado para alojar as aplicações Web front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de execução longa ou perpétuas, independentes da interação ou intervenção do utilizador.

Para obter mais informações, consulte [O que é um Serviço em Nuvem?].

> [!NOTE]
> *Pretende compilar um site simples?*
> Se o seu cenário envolver apenas um front-end de um site simples, considere utilizar a funcionalidade Aplicações Web simples do Serviço de Aplicações do Azure. Pode facilmente atualizar para uma Serviço em Nuvem à medida que o Web site cresce e os seus requisitos se alteram. Consulte os artigos do [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/) relativos ao desenvolvimento da funcionalidade Web Apps no App Service do Azure.
> <br />
> 
> 

## <a name="project-creation"></a>Criação do projeto
No Visual Studio, pode selecionar **Serviço em Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**.

![Caixa de Diálogo Novo Projeto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No assistente do Serviço em Nuvem do Azure, pode criar novas funções da Web e de trabalho.

![Caixa de Diálogo Serviço em Nuvem do Azure.](./media/cloud-services-python-ptvs/new-service-wizard.png)

O modelo de função de trabalho é fornecido com o código automático de ligação a uma conta de armazenamento do Azure ou ao Service Bus do Azure.

![Solução de Serviço em Nuvem](./media/cloud-services-python-ptvs/worker.png)

Pode adicionar funções da Web ou de trabalho a um serviço em nuvem existente em qualquer altura.  Pode optar por adicionar projetos existentes na sua solução ou criar novos.

![Adicionar Comando de Função](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

O serviço em nuvem pode conter funções implementadas em diferentes idiomas.  Por exemplo, pode ter uma função da Web do Python implementada utilizando Django, com o Python ou com funções de trabalho do C#.  Pode comunicar facilmente entre as funções de utilizar as filas do Service Bus ou as filas de armazenamento.

## <a name="install-python-on-the-cloud-service"></a>Instalar o Python no serviço em nuvem
> [!WARNING]
> Os scripts de configuração instalados com o Visual Studio (aquando da última atualização deste artigo) não funcionam. Esta secção descreve uma solução.
> 
> 

O problema principal com os scripts de configuração é o facto de não instalarem o python. Em primeiro lugar, defina duas [tarefas de arranque](cloud-services-startup-tasks.md) no ficheiro [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). A primeira tarefa (**PrepPython.ps1**) transfere e instala o tempo de execução do Python. A segunda tarefa (**PipInstaller.ps1**) é executa pip para instalar quaisquer dependências que possa ter.

Os seguintes scripts foram escritos como alvo python 3.8. Se pretender utilizar a versão Client 2. x do Python, defina o ficheiro variável **PYTHON2** como **Ligado** para as duas tarefas de arranque e a tarefa de tempo de execução: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

As variáveis **PYTHON2** e **PYPATH** têm de ser adicionadas à tarefa de arranque da função de trabalho. A variável **PYPATH** só é utilizada se a variável **PYTHON2** estiver definida como **Ligado**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>ServiceDefinition.csdef de amostra
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Em seguida, crie os ficheiros **PrepPython.ps1** e **PipInstaller.ps1** na pasta **. /bin** da sua função.

#### <a name="preppythonps1"></a>PrepPython.ps1
Este script instala o Python. Se a variável ambiente **PYTHON2** estiver **definida,** então o Python 2.7 está instalado, caso contrário, o Python 3.8 está instalado.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.8.8/python-3.8.8-amd64.exe"
        $outFile = "${env:TEMP}\python-3.8.8-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.18/python-2.7.18.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.18.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Este script chama o pip e instala todas as dependências no ficheiro **requirements.txt**. Se a variável ambiente **PYTHON2** estiver **definida,** então é utilizada a Python 2.7, caso contrário é utilizada a Python 3.8.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modificar o LaunchWorker.ps1
> [!NOTE]
> No caso de um projeto de uma **função de trabalho**, é preciso o ficheiro **LauncherWorker.ps1** para executar o ficheiro de arranque. Num projeto **função da Web**, o ficheiro de arranque é definido nas propriedades do projeto.
> 
> 

O **bin\LaunchWorker.ps1** foi criado originalmente para muito trabalho de preparação, mas realmente não funciona. Substitua o conteúdo nesse ficheiro pelo seguinte script.

Este script chama o ficheiro **worker.py** a partir do seu projeto de Python. Se a variável ambiente **PYTHON2** estiver **definida,** então é utilizada a Python 2.7, caso contrário é utilizada a Python 3.8.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Os modelos do Visual Studio criam um ficheiro **ps.cmd** na pasta **./bin**. Este script de shell chama os scripts de wrapper do PowerShell acima e fornece o registo com base no nome do wrapper de PowerShell chamado. Se este ficheiro não foi criado, eis o que deve aparecer no mesmo. 

```cmd
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Executar localmente
Se definir o seu projeto de serviço cloud como o projeto de arranque e premir F5, o serviço cloud é executado no emulador local do Azure.

Apesar de o PTVS suportar a iniciação no emulador, a depuração (por exemplo, pontos de interrupção) não funciona.

Para depurar as suas funções da Web e de trabalho, pode optar por definir o projeto de função como o projeto de arranque e efetuar a depuração.  Também pode definir vários projetos de arranque.  Clique com o botão direito do rato na solução e, em seguida, selecione **Definir Projetos de Arranque**.

![Propriedades do Projeto de Arranque da Solução](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicar no Azure
Para publicar, clique com o botão direito do rato no projeto do serviço em nuvem na solução e, em seguida, selecione **Publicar**.

![Início de Sessão de Publicação do Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Inicie o assistente. Se for necessário, ative o ambiente de trabalho remoto. O ambiente de trabalho remoto é útil quando necessita de depurar algo.

Quando tiver concluído a configuração de definições, clique em **Publicar**.

É apresentado algum progresso na janela de resultados e, em seguida, verá a janela de Registo de Atividades do Microsoft Azure.

![Janela de Registo de Atividades do Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

A implementação demora alguns minutos a ser concluída. Em seguida, as funções da Web e de trabalho são executadas no Azure!

### <a name="investigate-logs"></a>Investigar registos
Depois de a máquina virtual do serviço de nuvem arrancar e instalar o Python, pode procurar nos registos para localizar todas as mensagens de falha. Estes registos estão localizados na pasta **C:\Resources\Directory\\{role}\LogFiles**. **PrepPython.err.txt** tem, pelo menos, um erro quando o script tenta detetar se o Python está instalado e o **PipInstaller.err.txt** pode queixar-se de uma versão desatualizada do pip.

## <a name="next-steps"></a>Passos seguintes
Para obter informações mais detalhadas sobre como trabalhar com funções da Web e de trabalho nas Ferramentas do Python para Visual Studio, consulte a documentação das PTVS:

* [Projetos de Serviço Cloud][Cloud Service Projects]

Para obter mais detalhes sobre a utilização de serviços do Azure a partir das suas funções da Web e de trabalho, tais como utilizar o Armazenamento do Azure ou o Service Bus, consulte os seguintes artigos:

* [Serviço Blob][Blob Service]
* [Serviço Tabela][Table Service]
* [Serviço Fila][Queue Service]
* [Filas de autocarros de serviço][Service Bus Queues]
* [Tópicos de ônibus de serviço][Service Bus Topics]

<!--Link references-->

[O que é um Serviço em Nuvem?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: /visualstudio/python/
[Cloud Service Projects]: /visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.8 32-bit]: https://www.python.org/downloads/