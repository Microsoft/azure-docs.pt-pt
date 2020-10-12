---
title: Tarefas comuns de startups para serviços cloud Microsoft Docs
description: Fornece alguns exemplos de tarefas comuns de startup que pode querer desempenhar na sua função web de serviços na nuvem ou no papel de trabalhador.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: beebe60d70b7e4908bd3e9348fe815036d6955c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85920079"
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas de arranque do Serviço Comum de Nuvem
Este artigo fornece alguns exemplos de tarefas comuns de arranque que poderá querer executar no seu serviço de cloud. Pode utilizar tarefas de arranque para executar operações antes de começar uma função. As operações que poderá querer realizar incluem a instalação de um componente, o registo de componentes com COM, a definição de chaves de registo ou o início de um longo processo de funcionamento. 

Consulte [este artigo](cloud-services-startup-tasks.md) para entender como funcionam as tarefas de arranque e especificamente como criar as entradas que definem uma tarefa de arranque.

> [!NOTE]
> As tarefas de arranque não são aplicáveis às Máquinas Virtuais, apenas às funções web e trabalhadora do Cloud Service.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Defina variáveis ambientais antes de começar uma função
Se precisar de variáveis ambientais definidas para uma tarefa específica, utilize o elemento [Ambiente] dentro do elemento [Tarefa.]

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

As variáveis também podem usar um [valor Azure XPath válido](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implementação. Em vez de utilizar o `value` atributo, defina um elemento infantil [RoleInstanceValue.]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configurar startup IIS com AppCmd.exe
A [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) ferramenta de linha de comando pode ser usada para gerir as definições do IIS no arranque do Azure. *AppCmd.exe* fornece acesso conveniente e de linha de comando às definições de configuração para utilização em tarefas de arranque no Azure. Utilizando *AppCmd.exe*, as definições do Website podem ser adicionadas, modificadas ou removidas para aplicações e sites.

No entanto, há algumas coisas a ter em atenção no uso de *AppCmd.exe* como uma tarefa de arranque:

* As tarefas de arranque podem ser executadas mais de uma vez entre reinicializações. Por exemplo, quando um papel recicla.
* Se uma * açãoAppCmd.exe* for executada mais de uma vez, pode gerar um erro. Por exemplo, tentar adicionar uma secção a *Web.config* duas vezes pode gerar um erro.
* As tarefas de arranque falham se devolverem um código de saída não zero ou **um nível de erro**. Por exemplo, quando *AppCmd.exe* gera um erro.

É uma boa prática verificar o **nível de erro** depois de ligar *AppCmd.exe*, o que é fácil de fazer se embrulhe a chamada para *AppCmd.exe* com um ficheiro *.cmd.* Se detetar uma resposta de **nível de erro** conhecida, pode ignorá-la ou passá-la de volta.

O nível de erro devolvido por *AppCmd.exe* estão listados no ficheiro winerror.h, podendo também ser vistos na [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gestão do nível de erro
Este exemplo adiciona uma secção de compressão e uma entrada de compressão para JSON no ficheiro *Web.config, * com manipulação de erros e registo.

As secções relevantes do ficheiro [ServiceDefinition.csdef] são apresentadas aqui, que incluem a definição do atributo [de execuçãoContexto](/previous-versions/azure/reference/gg557552(v=azure.100)#task) `elevated` para dar a *AppCmd.exe* permissões suficientes para alterar as definições no ficheiro *Web.config:*

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

O ficheiro de lote *De arranque.cmd* utiliza *AppCmd.exe* para adicionar uma secção de compressão e uma entrada de compressão para json no ficheiro *Web.config.* O **nível de erro** esperado de 183 está definido para zero usando o VERIFY.EXE programa de linha de comando. Os velóis de erro inesperados são registados em StartupErrorLog.txt.

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

## <a name="add-firewall-rules"></a>Adicionar regras de firewall
Em Azure, existem efetivamente duas firewalls. A primeira firewall controla as ligações entre a máquina virtual e o mundo exterior. Esta firewall é controlada pelo elemento [EndPoints] no ficheiro [ServiceDefinition.csdef.]

A segunda firewall controla as ligações entre a máquina virtual e os processos dentro dessa máquina virtual. Esta firewall pode ser controlada pela `netsh advfirewall firewall` ferramenta da linha de comando.

O Azure cria regras de firewall para os processos iniciados dentro das suas funções. Por exemplo, quando inicia um serviço ou programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que esse serviço se comunique com a Internet. No entanto, se criar um serviço que seja iniciado por um processo fora da sua função (como um serviço COM+ ou uma Tarefa Agendada do Windows), tem de criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Estas regras de firewall podem ser criadas utilizando uma tarefa de arranque.

Uma tarefa de arranque que crie uma regra de firewall deve ter uma tarefa[de] texto de [execução] **elevada**. Adicione a seguinte tarefa de arranque ao ficheiro [ServiceDefinition.csdef.]

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

Para adicionar a regra de firewall, deve utilizar os `netsh advfirewall firewall` comandos apropriados no seu ficheiro de lote de arranque. Neste exemplo, a tarefa de arranque requer segurança e encriptação para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloqueie um endereço IP específico
Pode restringir o acesso de uma função web Azure a um conjunto de endereços IP especificados modificando o seu ficheiro **IISweb.config.** Também é necessário utilizar um ficheiro de comando que desbloqueie a secção **ipSecurity** do ficheiro **ApplicationHost.config.**

Para fazer desbloquear a secção **ipSecurity** do ficheiro **ApplicationHost.config, ** crie um ficheiro de comando que seja executado no início da função. Crie uma pasta ao nível da raiz da sua função web chamada **startup** e, dentro desta pasta, crie um ficheiro de lote chamado **startup.cmd**. Adicione este ficheiro ao seu projeto Visual Studio e desemine as propriedades para **Copy Always** para garantir que está incluído no seu pacote.

Adicione a seguinte tarefa de arranque ao ficheiro [ServiceDefinition.csdef.]

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

Esta tarefa faz com que o ficheiro de lote **de startup.cmd** seja executado sempre que a função web é inicializada, garantindo que a secção **ipSecurity** necessária é desbloqueada.

Por fim, modifique a [secção system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) do ficheiro **web.config** da sua função web para adicionar uma lista de endereços IP que tenham acesso, como mostra o seguinte exemplo:

Esta amostra config **permite que** todos os IPs acedam ao servidor, exceto os dois definidos

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

Esta amostra config **nega a** todos os IPs de aceder ao servidor, exceto para os dois definidos.

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

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de arranque PowerShell
Os scripts do Windows PowerShell não podem ser chamados diretamente do ficheiro [ServiceDefinition.csdef,] mas podem ser invocados a partir de um ficheiro de lote de arranque.

PowerShell (por defeito) não funciona scripts não assinados. A menos que assine o seu script, precisa configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, a **Política de Execução** tem de ser definida como **Sem Restrições**. A definição **de Política de Execução** que utiliza baseia-se na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se estiver a utilizar um SISTEMA Convidado que executa o PowerShell 2.0 ou 1.0 pode forçar a versão 2 a ser executada e, se não estiver disponível, utilize a versão 1.

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
Pode utilizar um recurso de armazenamento local para armazenar ficheiros criados pela sua tarefa de arranque que é acedida mais tarde pela sua aplicação.

Para criar o recurso de armazenamento local, adicione uma secção [LocalResources] ao ficheiro [ServiceDefinition.csdef] e, em seguida, adicione o elemento infantil [LocalStorage.] Dê ao recurso de armazenamento local um nome único e um tamanho adequado para a sua tarefa de arranque.

Para utilizar um recurso de armazenamento local na sua tarefa de arranque, precisa de criar uma variável ambiental para fazer referência à localização do recurso de armazenamento local. Em seguida, a tarefa de arranque e a aplicação são capazes de ler e escrever ficheiros para o recurso de armazenamento local.

As secções relevantes do ficheiro **ServiceDefinition.csdef** são apresentadas aqui:

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

Como exemplo, este ficheiro de lote **Startup.cmd** utiliza a variável **ambiente PathToStartupStorage** para criar o ficheiro **MyTest.txt** no local de armazenamento local.

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

## <a name="run-in-the-emulator-or-cloud"></a>Corra no emulador ou na nuvem
Pode fazer com que a sua tarefa de arranque execute diferentes passos quando estiver a operar na nuvem em comparação com quando está no emulador de computação. Por exemplo, pode querer utilizar uma cópia nova dos seus dados SQL apenas quando estiver a correr no emulador. Ou pode querer fazer algumas otimizações de desempenho para a nuvem que não precisa de fazer quando corre no emulador.

Esta capacidade de executar diferentes ações no emulador computacional e na nuvem pode ser realizada criando uma variável ambiental no ficheiro [ServiceDefinition.csdef.] Em seguida, você testa essa variável ambiente para um valor na sua tarefa de arranque.

Para criar a variável [Variable]ambiente, adicione o elemento / [Variable RoleInstanceValue] e crie um valor XPath de `/RoleEnvironment/Deployment/@emulated` . O valor da variável ambiente **%ComputeEmulatorRunning%** é `true` quando funciona no emulador de computação e `false` quando funciona na nuvem.

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

A tarefa pode agora verificar a variável ambiente **%ComputeEmulatorRunning%** para executar diferentes ações com base no facto de o papel estar a ser praticado na nuvem ou no emulador. Aqui está um script de concha .cmd que verifica a variável do ambiente.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detete que a sua tarefa já está executada
A função pode reciclar sem um reboot fazendo com que as suas tarefas de arranque voltem a funcionar. Não há nenhuma bandeira que indique que uma tarefa já foi executada no VM de acolhimento. Podes ter algumas tarefas em que não importa que sejam executadas várias vezes. No entanto, pode deparou-se com uma situação em que precisa de evitar que uma tarefa seja executada mais de uma vez.

A forma mais simples de detetar que uma tarefa já foi executada é criar um ficheiro na pasta **%TEMP%** quando a tarefa tiver sucesso e procurá-la no início da tarefa. Aqui está uma amostra de guião de conchas cmd que faz isso por si.

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

## <a name="task-best-practices"></a>Boas práticas de tarefa
Aqui estão algumas boas práticas que deve seguir ao configurar a tarefa para o seu papel web ou trabalhador.

### <a name="always-log-startup-activities"></a>Sempre registar atividades de startup
O Visual Studio não fornece um depurgger para passar por ficheiros de lote, por isso é bom obter o máximo de dados possível sobre o funcionamento dos ficheiros de lote. Registar a saída dos ficheiros de lote, tanto **de stdout** como **de stderr,** pode dar-lhe informações importantes ao tentar depurar e corrigir ficheiros de lote. Para registar tanto **o stdout** como o **stderr** no ficheiro StartupLog.txt no diretório apontado pela variável ambiente **%TEMP%,** adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao fim das linhas específicas que pretende registar. Por exemplo, executar setup.exe no **diretório %PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Para simplificar o seu xml, pode criar um ficheiro *CMD* de invólucro que liga para todas as suas tarefas de arranque juntamente com o registo e garante que cada tarefa infantil partilha as mesmas variáveis ambientais.

Pode ser irritante usá-lo `>> "%TEMP%\StartupLog.txt" 2>&1` no final de cada tarefa de arranque. Pode impor a exploração de tarefas criando um invólucro que lida com o registo de registos para si. Este invólucro chama o ficheiro de lote real que pretendes executar. Qualquer saída do ficheiro do lote-alvo será redirecionada para o ficheiro *Startuplog.txt.*

O exemplo a seguir mostra como redirecionar toda a saída de um ficheiro de lote de arranque. Neste exemplo, o ficheiro ServerDefinition.csdef cria uma tarefa de arranque que chama *logwrap.cmd*. *logwrap.cmd* chama *Startup2.cmd,* redirecionando toda a produção para **%TEMP% \\StartupLog.txt**.

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

Saída de amostra no ficheiro **StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O ficheiro **StartupLog.txt** está localizado na pasta *C:\Resources\temp \\ {identificador de função}\RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir execuçõesContexto adequadamente para tarefas de arranque
Desateia os privilégios adequadamente para a tarefa de arranque. Por vezes, as tarefas de arranque devem ser executadas com privilégios elevados, mesmo que o papel seja executado com privilégios normais.

O [atributo Tarefa de Tarefa de execução Dedistexto][Task] define o nível de privilégio da tarefa de arranque. Usar `executionContext="limited"` significa que a tarefa de arranque tem o mesmo nível de privilégio que o papel. A utilização `executionContext="elevated"` de meios, a tarefa de arranque tem privilégios de administrador, o que permite que a tarefa de arranque execute tarefas de administrador sem dar privilégios ao administrador para o seu papel.

Um exemplo de uma tarefa de startup que requer privilégios elevados é uma tarefa de startup que usa **AppCmd.exe** para configurar o IIS. **AppCmd.exe** `executionContext="elevated"` requer.

### <a name="use-the-appropriate-tasktype"></a>Utilize o tipo de tarefa apropriado
O atributo[TaskType] de [tarefa]determina a forma como a tarefa de arranque é executada. Existem três valores: **simples,** **fundo**e **primeiro plano.** As tarefas de fundo e de primeiro plano são iniciadas de forma assíncronea, e então as tarefas simples são executadas sincronizadamente uma de cada vez.

Com tarefas **simples** de arranque, pode definir a ordem em que as tarefas executadas pela ordem em que as tarefas estão listadas no ficheiro ServiceDefinition.csdef. Se uma tarefa **simples** termina com um código de saída sem zero, então o procedimento de arranque para e a função não começa.

A diferença entre tarefas de startup de **fundo** e tarefas de arranque **em primeiro plano** é que as tarefas de primeiro **plano** mantêm o papel em execução até que a tarefa de **primeiro plano** termine. Isto também significa que, se a tarefa **de primeiro plano** estiver pendurada ou se falhar, a função não recicla até que a tarefa de primeiro **plano** seja forçada a fechar. Por esta razão, são recomendadas tarefas **de fundo** para tarefas de arranque assíncronas, a menos que precise dessa funcionalidade da tarefa de **primeiro plano.**

### <a name="end-batch-files-with-exit-b-0"></a>Fim dos ficheiros de lote com EXIT /B 0
A função só começará se o **nível** de erro de cada uma das suas tarefas de arranque simples for zero. Nem todos os programas definem corretamente o **nível de erro** (código de saída), pelo que o ficheiro do lote deve terminar com um `EXIT /B 0` se tudo correr corretamente.

Um desaparecimento `EXIT /B 0` no final de um ficheiro de lote de arranque é uma causa comum de funções que não começam.

> [!NOTE]
> Reparei que os ficheiros de lote aninhados às vezes param de responder quando usam o `/B` parâmetro. Pode querer certificar-se de que este problema não acontece se outro ficheiro de lote ligar para o ficheiro de lote atual, como se utilizar o [invólucro de registo](#always-log-startup-activities). Pode omitir o `/B` parâmetro neste caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Espere que as tarefas de arranque sejam executadas mais de uma vez
Nem todas as reciclagems de papéis incluem um reboot, mas todas as reciclagems de papéis incluem executar todas as tarefas de arranque. Isto significa que as tarefas de arranque devem ser capazes de executar várias vezes entre reboots sem qualquer problema. Isto é discutido na [secção anterior.](#detect-that-your-task-has-already-run)

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Use o armazenamento local para armazenar ficheiros que devem ser acedidos na função
Se pretender copiar ou criar um ficheiro durante a sua tarefa inicial que esteja então acessível à sua função, esse ficheiro deve ser colocado no armazenamento local. Consulte [a secção anterior.](#create-files-in-local-storage-from-a-startup-task)

## <a name="next-steps"></a>Passos seguintes
Reveja o [modelo e pacote de serviços](cloud-services-model-and-package.md) em nuvem

Saiba mais sobre como as [Tarefas](cloud-services-startup-tasks.md) funcionam.

[Crie e implemente](cloud-services-how-to-create-deploy-portal.md) o seu pacote de serviço em nuvem.

[ServiçoDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Pontos Finais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Recursos Locais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
