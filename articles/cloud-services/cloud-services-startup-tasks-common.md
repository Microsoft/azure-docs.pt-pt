---
title: Tarefas comuns de startups para serviços de nuvem / Microsoft Docs
description: Fornece alguns exemplos de tarefas comuns de startups que você pode querer realizar no seu papel web de serviços na nuvem ou papel de trabalhador.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378947"
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas de arranque do Serviço Comum cloud
Este artigo fornece alguns exemplos de tarefas comuns de arranque que você pode querer realizar no seu serviço na nuvem. Pode utilizar tarefas de arranque para realizar operações antes de iniciar uma função. As operações que desejare efetuar incluem a instalação de um componente, o registo de componentes COM, a definição de teclas de registo ou o início de um processo de longo prazo. 

Veja [este artigo](cloud-services-startup-tasks.md) para entender como funcionam as tarefas de arranque e especificamente como criar as entradas que definem uma tarefa de arranque.

> [!NOTE]
> As tarefas de arranque não são aplicáveis às Máquinas Virtuais, apenas às funções Cloud Service Web e Worker.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definir variáveis ambientais antes do início de um papel
Se necessitar de variáveis ambientais definidas para uma tarefa específica, utilize o elemento [Ambiente] dentro do elemento [Tarefa]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

As variáveis também podem usar um [valor Azure XPath válido](cloud-services-role-config-xpath.md) para referência algo sobre a implementação. Em vez de utilizar o atributo `value`, defina um elemento infantil [RoleInstanceValue]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configure startup IIS com AppCmd.exe
A ferramenta de linha de comando [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) pode ser usada para gerir as definições do IIS no arranque do Azure. *AppCmd.exe* fornece acesso conveniente e de linha de comando às definições de configuração para utilização em tarefas de arranque no Azure. Utilizando *AppCmd.exe,* as configurações do site podem ser adicionadas, modificadas ou removidas para aplicações e sites.

No entanto, existem algumas coisas a ter em atenção na utilização do *AppCmd.exe* como tarefa de arranque:

* As tarefas de arranque podem ser executadas mais de uma vez entre reboots. Por exemplo, quando um papel recicla.
* Se uma ação *AppCmd.exe* for executada mais de uma vez, pode gerar um erro. Por exemplo, tentar adicionar uma secção a *Web.config* duas vezes pode gerar um erro.
* As tarefas de arranque falham se devolverem um código de saída não zero ou **um nível**de erro . Por exemplo, quando *appCmd.exe* gera um erro.

É uma boa prática verificar o **nível de erro** depois de ligar para *AppCmd.exe*, o que é fácil de fazer se embrulhar a chamada para *AppCmd.exe* com um ficheiro *.cmd.* Se detetar uma resposta de **nível** de erro conhecida, pode ignorá-la ou passá-la de volta.

O nível de erro devolvido por *AppCmd.exe* está listado no ficheiro winerror.h, e também pode ser visto no [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gestão do nível de erro
Este exemplo adiciona uma secção de compressão e uma entrada de compressão para jSON ao ficheiro *Web.config,* com manuseamento de erros e registo.

As secções relevantes do ficheiro [ServiceDefinition.csdef] são mostradas aqui, que incluem a definição do atributo [de execuçãoContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) a `elevated` para dar permissões suficientes para *appCmd.exe* suficientes para alterar as definições no ficheiro *Web.config:*

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

O ficheiro de lote *Startup.cmd* utiliza *AppCmd.exe* para adicionar uma secção de compressão e uma entrada de compressão para a JSON ao ficheiro *Web.config.* O **nível** de erro esperado de 183 está definido para zero utilizando o CHECK. Programa de linha de comando EXE. Níveis de erro inesperados são registados em StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Adicione regras de firewall
Em Azure, existem efetivamente duas firewalls. A primeira firewall controla as ligações entre a máquina virtual e o mundo exterior. Esta firewall é controlada pelo elemento [Pontos finais] no ficheiro [ServiceDefinition.csdef]

A segunda firewall controla as ligações entre a máquina virtual e os processos dentro dessa máquina virtual. Esta firewall pode ser controlada pela ferramenta de linha de comando `netsh advfirewall firewall`.

O Azure cria regras de firewall para os processos iniciados dentro das suas funções. Por exemplo, quando inicia um serviço ou programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que esse serviço se comunique com a Internet. No entanto, se criar um serviço que seja iniciado por um processo fora da sua função (como um serviço COM+ ou uma Tarefa Agendada do Windows), precisa de criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Estas regras de firewall podem ser criadas utilizando uma tarefa de arranque.

Uma tarefa de arranque que crie uma regra de firewall deve ter uma[tarefa] de [execução Context]task de **elevado**. Adicione a seguinte tarefa de arranque ao ficheiro [ServiceDefinition.csdef]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Para adicionar a regra da firewall, deve utilizar os comandos de `netsh advfirewall firewall` adequados no ficheiro de lote de arranque. Neste exemplo, a tarefa de arranque requer segurança e encriptação para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloqueie um endereço IP específico
Pode restringir o acesso de uma função web Azure a um conjunto de endereços IP especificados modificando o seu ficheiro **Web.config** IIS. Também precisa de utilizar um ficheiro de comando que desbloqueia a secção **ipSecurity** do ficheiro **ApplicationHost.config.**

Para desbloquear a secção **ipSecurity** do ficheiro **ApplicationHost.config,** crie um ficheiro de comando que seja executado no início da função. Crie uma pasta ao nível raiz da sua função web chamada **startup** e, dentro desta pasta, crie um ficheiro de lote chamado **startup.cmd**. Adicione este ficheiro ao seu projeto Visual Studio e detete as propriedades para **Copiar Sempre** para garantir que está incluído no seu pacote.

Adicione a seguinte tarefa de arranque ao ficheiro [ServiceDefinition.csdef]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adicione este comando ao ficheiro **startup.cmd:**

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Esta tarefa faz com que o ficheiro de lote **startup.cmd** seja executado sempre que a função web é inicializada, garantindo que a secção **ipSecurity** necessária é desbloqueada.

Por fim, modifique a [secção system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) do ficheiro **web.config** do seu papel web para adicionar uma lista de endereços IP que têm acesso, como mostra o seguinte exemplo:

Esta amostra config **permite** que todos os IPs acedam ao servidor exceto os dois definidos

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta amostra **config nega** que todos os IPs acedam ao servidor, exceto os dois definidos.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de arranque powerShell
Os scripts Do Windows PowerShell não podem ser chamados diretamente a partir do ficheiro [ServiceDefinition.csdef] mas podem ser invocados a partir de um ficheiro de lote de arranque.

PowerShell (por padrão) não executa scripts não assinados. A menos que assine o seu script, precisa configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, a Política de **Execução** deve ser definida para **Sem Restrições**. A definição **De Execução Política** que utiliza baseia-se na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se estiver a utilizar um Os convidado que executa o PowerShell 2.0 ou 1.0, pode forçar a versão 2 a ser executada e, se não estiver disponível, utilize a versão 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Criar ficheiros no armazenamento local a partir de uma tarefa de arranque
Pode utilizar um recurso de armazenamento local para armazenar ficheiros criados pela sua tarefa de arranque que é acedido posteriormente pela sua aplicação.

Para criar o recurso de armazenamento local, adicione uma secção [Recursos Locais] ao ficheiro [ServiceDefinition.csdef] e, em seguida, adicione o elemento infantil [Armazenamento Local] Dê ao recurso de armazenamento local um nome único e um tamanho adequado para a sua tarefa de arranque.

Para utilizar um recurso de armazenamento local na sua tarefa de arranque, precisa criar uma variável ambiental para fazer referência à localização local do recurso de armazenamento. Em seguida, a tarefa de arranque e a aplicação são capazes de ler e escrever ficheiros para o recurso de armazenamento local.

As secções relevantes do ficheiro **ServiceDefinition.csdef** são mostradas aqui:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Como exemplo, este ficheiro de lote **Startup.cmd** utiliza a variável ambiente **PathToStartupStorage** para criar o ficheiro **MyTest.txt** no local de armazenamento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Pode aceder à pasta de armazenamento local a partir do Azure SDK utilizando o método [GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Corra no emulador ou nuvem
Pode ter a sua tarefa de arranque a executar diferentes passos quando está a funcionar na nuvem em comparação com quando está no emulador de cálculo. Por exemplo, é melhor utilizar uma cópia nova dos seus dados SQL apenas quando estiver em execução no emulador. Ou talvez queira fazer algumas otimizações de desempenho para a nuvem que não precisa fazer ao correr no emulador.

Esta capacidade de realizar diferentes ações no emulador de computação e na nuvem pode ser realizada através da criação de uma variável ambiental no ficheiro [ServiceDefinition.csdef] Em seguida, testa essa variável ambiental por um valor na sua tarefa de arranque.

Para criar a variável ambiental, adicione o elemento [Variável]/[RoleInstanceValue] e crie um valor XPath de `/RoleEnvironment/Deployment/@emulated`. O valor da variável ambiente **%ComputeEmulatorRunning%** é `true` quando se executa no emulador de computação, e `false` quando se corre na nuvem.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

A tarefa pode agora verificar a variável ambiente **%ComputeEmulatorRunning%** para realizar diferentes ações com base no facto de o papel estar a funcionar na nuvem ou no emulador. Aqui está um script de concha .cmd que verifica para essa variável ambiental.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detete que a sua tarefa já correu
A função pode reciclar sem um reboot fazendo com que as suas tarefas de arranque voltem a funcionar. Não há nenhuma bandeira que indique que uma tarefa já tenha sido executada no VM anfitrião. Podes ter algumas tarefas em que não importa que sejam executadas várias vezes. No entanto, pode encontrar-se numa situação em que precisa de evitar que uma tarefa corra mais do que uma vez.

A forma mais simples de detetar que uma tarefa já executou é criar um ficheiro na pasta **%TEMP%** quando a tarefa for bem sucedida e procurá-la no início da tarefa. Aqui está uma amostra de um guião de concha cmd que faz isso por si.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>As melhores práticas de tarefa
Aqui estão algumas das melhores práticas que deve seguir ao configurar a tarefa para a sua função web ou trabalhador.

### <a name="always-log-startup-activities"></a>Sempre registre atividades de startups
O Visual Studio não fornece um debugger para passar por ficheiros de lote, por isso é bom obter o máximo de dados sobre o funcionamento dos ficheiros de lote. Registar a saída de ficheiros de lote, tanto **stdout** como **stderr,** pode dar-lhe informações importantes ao tentar depurar e corrigir ficheiros de lote. Para registar tanto o **stdout** como o **stderr** para o ficheiro StartupLog.txt no diretório apontado pela variável ambiente **%TEMP%,** adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao fim das linhas específicas que pretende registar. Por exemplo, executar configuração.exe no diretório **%PathToApp1Install%** :

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Para simplificar o seu xml, pode criar um ficheiro *de cmd* de invólucro que ligue todas as suas tarefas de arranque juntamente com a exploração madeireira e garanta que cada tarefa infantil partilha as mesmas variáveis ambientais.

Pode achar irritante, no entanto, usá`>> "%TEMP%\StartupLog.txt" 2>&1` no final de cada tarefa de arranque. Pode impor o registo de tarefas criando um invólucro que manuseie o registo para si. Este invólucro chama o verdadeiro ficheiro de lote que quer executar. Qualquer saída do ficheiro de lote alvo será redirecionada para o ficheiro *Startuplog.txt.*

O exemplo que se segue mostra como redirecionar toda a produção de um ficheiro de lote de arranque. Neste exemplo, o ficheiro ServerDefinition.csdef cria uma tarefa de arranque que chama *logwrap.cmd*. *logwrap.cmd* chama *Startup2.cmd,* redirecionando toda a produção para **%TEMP%\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Saída da amostra no ficheiro **StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O ficheiro **StartupLog.txt** está localizado na pasta *C:\Resources\temp\\{role identifier}RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir execuçãoContexto adequadamente para tarefas de arranque
Detete privilégios adequadamente para a tarefa de arranque. Por vezes, as tarefas de arranque devem funcionar com privilégios elevados, mesmo que o papel seja executado com privilégios normais.

O atributo [de execuçãoContext][tarefa] define o nível de privilégio da tarefa de arranque. Usar `executionContext="limited"` significa que a tarefa de arranque tem o mesmo nível de privilégio que o papel. Usar `executionContext="elevated"` significa que a tarefa de arranque tem privilégios de administrador, o que permite que a tarefa de arranque execute tarefas de administrador sem dar privilégios ao administrador para o seu papel.

Um exemplo de uma tarefa de arranque que requer privilégios elevados é uma tarefa de arranque que utiliza **appCmd.exe** para configurar o IIS. **AppCmd.exe** requer `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Utilize o tipo de tarefa apropriado
O atributo [taskType][tarefa] determina a forma como a tarefa de arranque é executada. Existem três valores: **simples,** **fundo,** e **primeiro plano.** As tarefas de fundo e primeiro plano são iniciadas assincronicamente, e então as tarefas simples são executadas sincronizadamente uma de cada vez.

Com **tarefas simples** de arranque, pode definir a ordem na qual as tarefas executadas pela ordem em que as tarefas estão listadas no ficheiro ServiceDefinition.csdef. Se uma tarefa **simples** terminar com um código de saída não zero, então o procedimento de arranque para e a função não começa.

A diferença entre as tarefas de arranque de **fundo** e as tarefas de arranque em **primeiro plano** é que as tarefas de primeiro **plano** mantêm o papel em funcionamento até que a tarefa de **primeiro plano** termine. Isto significa também que, se a tarefa de **primeiro plano** se der ou falhar, o papel não reciclará até que a tarefa de **primeiro plano** seja forçada a ser encerrada. Por esta razão, são recomendadas tarefas de **fundo** para tarefas de arranque assíncronas, a menos que necessite dessa funcionalidade da tarefa de **primeiro plano.**

### <a name="end-batch-files-with-exit-b-0"></a>Fim dos ficheiros do lote com SAÍDA /B 0
A função só começará se o **nível** de erro de cada uma das suas simples tarefas de arranque for zero. Nem todos os programas configuram corretamente o **nível de erro** (código de saída), pelo que o ficheiro do lote deve terminar com uma `EXIT /B 0` se tudo correr corretamente.

Uma `EXIT /B 0` em falta no final de um ficheiro de lote de startups é uma causa comum de papéis que não começam.

> [!NOTE]
> Reparei que os ficheiros de lotes aninhados às vezes penduram-se quando se usa o parâmetro `/B`. É dever certificar-se de que este problema de espera não acontece se outro ficheiro de lote ligar para o ficheiro atual do lote, como se utilizar o invólucro de [registo](#always-log-startup-activities). Pode omiti-lo o parâmetro `/B` neste caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Espere que as tarefas de arranque sejam executadas mais de uma vez
Nem todos os recicladores de funções incluem um reboot, mas todos os recicles de funções incluem executar todas as tarefas de arranque. Isto significa que as tarefas de arranque devem ser executadas várias vezes entre reboots sem problemas. Isto é discutido na [secção anterior](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Utilize armazenamento local para armazenar ficheiros que devem ser acedidos na função
Se pretender copiar ou criar um ficheiro durante a sua tarefa de arranque que seja então acessível à sua função, então esse ficheiro deve ser colocado no armazenamento local. Consulte a [secção anterior](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Passos Seguintes
Reveja o [modelo e pacote](cloud-services-model-and-package.md) de serviço na nuvem

Saiba mais sobre como funcionam [as Tarefas.](cloud-services-startup-tasks.md)

[Crie e implante](cloud-services-how-to-create-deploy-portal.md) o seu pacote de serviço na nuvem.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Pontos finais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[Armazenamento Local]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Recursos Locais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



