---
title: Tarefas de inicialização comuns para serviços de nuvem | Microsoft Docs
description: Fornece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar em sua função Web de serviços de nuvem ou função de trabalho.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 2eb299ad841444a3100eac207b225d5377959f85
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358950"
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas comuns de inicialização do serviço de nuvem
Este artigo fornece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar em seu serviço de nuvem. Você pode usar tarefas de inicialização para executar operações antes de uma função ser iniciada. As operações que você pode querer executar incluem a instalação de um componente, o registro de componentes COM, a definição de chaves do registro ou o início de um processo de execução demorada. 

Consulte [Este artigo](cloud-services-startup-tasks.md) para entender como funcionam as tarefas de inicialização e, especificamente, como criar as entradas que definem uma tarefa de inicialização.

> [!NOTE]
> As tarefas de inicialização não são aplicáveis às máquinas virtuais, somente às funções Web e de trabalho do serviço de nuvem.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definir variáveis de ambiente antes de uma função ser iniciada
Se você precisar de variáveis de ambiente definidas para uma tarefa específica, use o elemento [Ambiente] dentro do elemento [Tarefa] .

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

As variáveis também podem usar um [valor XPath válido do Azure](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implantação. Em vez de usar `value` o atributo, defina um elemento filho [RoleInstanceValue] .

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configurar a inicialização do IIS com AppCmd. exe
A ferramenta de linha de comando [Appcmd. exe](https://technet.microsoft.com/library/jj635852.aspx) pode ser usada para gerenciar as configurações do IIS na inicialização no Azure. O *Appcmd. exe* fornece acesso de linha de comando conveniente às definições de configuração para uso em tarefas de inicialização no Azure. Usando o *Appcmd. exe*, as configurações do site podem ser adicionadas, modificadas ou removidas para aplicativos e sites.

No entanto, há algumas coisas a serem observadas no uso de *Appcmd. exe* como uma tarefa de inicialização:

* As tarefas de inicialização podem ser executadas mais de uma vez entre as reinicializações. Por exemplo, quando uma função é reciclada.
* Se uma ação *Appcmd. exe* for executada mais de uma vez, ela poderá gerar um erro. Por exemplo, tentar adicionar uma seção a *Web. config* duas vezes poderia gerar um erro.
* As tarefas de inicialização falharão se retornarem um código de saída diferente de zero ou **ERRORLEVEL**. Por exemplo, quando *Appcmd. exe* gera um erro.

É uma boa prática verificar o **ERRORLEVEL** depois de chamar *Appcmd. exe*, o que é fácil se você encapsular a chamada para *Appcmd. exe* com um arquivo *. cmd* . Se você detectar uma resposta **ERRORLEVEL** conhecida, poderá ignorá-la ou passá-la de volta.

O ERRORLEVEL retornado por *Appcmd. exe* está listado no arquivo Winerror. h e também pode ser visto no [msdn](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gerenciamento do nível de erro
Este exemplo adiciona uma seção de compactação e uma entrada de compactação para JSON ao arquivo *Web. config* , com tratamento de erros e registro em log.

As seções relevantes do arquivo de @ [ServiceDefinition.csdef] são mostradas aqui, que incluem a [](/previous-versions/azure/reference/gg557552(v=azure.100)#task) definição do atributo `elevated` ExecutionContext como para dar ao *Appcmd. exe* permissões suficientes para alterar as configurações no  *Arquivo Web. config* :

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

O arquivo em lotes *Startup. cmd* usa *Appcmd. exe* para adicionar uma seção de compactação e uma entrada de compactação para JSON ao arquivo *Web. config* . O **ERRORLEVEL** esperado de 183 é definido como zero usando a verificação. Programa de linha de comando EXE. Errorlevels inesperados são registrados em StartupErrorLog. txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

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
No Azure, há efetivamente dois firewalls. O primeiro Firewall controla as conexões entre a máquina virtual e o mundo exterior. Esse firewall é controlado pelo elemento de [Extremidade] no arquivo de imdefinição [ServiceDefinition.csdef] .

O segundo Firewall controla as conexões entre a máquina virtual e os processos dentro dessa máquina virtual. Esse firewall pode ser controlado pela ferramenta `netsh advfirewall firewall` de linha de comando.

O Azure cria regras de firewall para os processos iniciados em suas funções. Por exemplo, quando você inicia um serviço ou programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que esse serviço se comunique com a Internet. No entanto, se você criar um serviço que é iniciado por um processo fora de sua função (como um serviço COM+ ou uma tarefa agendada do Windows), será necessário criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Essas regras de firewall podem ser criadas usando uma tarefa de inicialização.

Uma tarefa de inicialização que cria uma regra de firewall deve ter uma[tarefa] [ExecutionContext]com **privilégios elevados**. Adicione a seguinte tarefa de inicialização ao arquivo de [ServiceDefinition.csdef] .

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

Para adicionar a regra de firewall, você deve usar os `netsh advfirewall firewall` comandos apropriados em seu arquivo em lotes de inicialização. Neste exemplo, a tarefa de inicialização requer segurança e criptografia para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloquear um endereço IP específico
Você pode restringir um acesso de função Web do Azure a um conjunto de endereços IP especificados modificando o arquivo **Web. config** do IIS. Você também precisa usar um arquivo de comando que desbloqueie a seção **ipSecurity** do arquivo **ApplicationHost. config** .

Para desbloquear a seção **ipSecurity** do arquivo **ApplicationHost. config** , crie um arquivo de comando que é executado no início da função. Crie uma pasta no nível raiz da função Web chamada **Startup** e, dentro dessa pasta, crie um arquivo em lotes chamado **Startup. cmd**. Adicione esse arquivo ao seu projeto do Visual Studio e defina as propriedades como **copiar sempre** para garantir que ele seja incluído no pacote.

Adicione a seguinte tarefa de inicialização ao arquivo de [ServiceDefinition.csdef] .

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

Adicione este comando ao arquivo **Startup. cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Essa tarefa faz com que o arquivo em lotes **Startup. cmd** seja executado toda vez que a função Web é inicializada, garantindo que a seção **ipSecurity** necessária seja desbloqueada.

Por fim, modifique a [seção System. WebServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) do arquivo **Web. config** de sua função Web para adicionar uma lista de endereços IP que recebem acesso, conforme mostrado no exemplo a seguir:

Esta configuração de exemplo **permite que** todos os IPs acessem o servidor, exceto os dois definidos

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

Esta configuração de exemplo **nega** que todos os IPs acessem o servidor, exceto os dois definidos.

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

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de inicialização do PowerShell
Os scripts do Windows PowerShell não podem ser chamados diretamente do arquivo de [ServiceDefinition.csdef] , mas podem ser invocados de dentro de um arquivo em lotes de inicialização.

O PowerShell (por padrão) não executa scripts não assinados. A menos que você assine o script, você precisa configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, o **ExecutionPolicy** deve ser definido como **irrestrito**. A configuração **ExecutionPolicy** que você usa baseia-se na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se você estiver usando um sistema operacional convidado que é executado no PowerShell 2,0 ou 1,0, você pode forçar a execução da versão 2 e, se não estiver disponível, use a versão 1.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Criar arquivos no armazenamento local de uma tarefa de inicialização
Você pode usar um recurso de armazenamento local para armazenar arquivos criados por sua tarefa de inicialização que é acessada posteriormente pelo seu aplicativo.

Para criar o recurso de armazenamento local, adicione uma seção [LocalResources] ao arquivo @ [ServiceDefinition.csdef] e adicione o elemento filho [localStorage] . Dê ao recurso de armazenamento local um nome exclusivo e um tamanho apropriado para a tarefa de inicialização.

Para usar um recurso de armazenamento local em sua tarefa de inicialização, você precisa criar uma variável de ambiente para fazer referência ao local do recurso de armazenamento local. Em seguida, a tarefa de inicialização e o aplicativo são capazes de ler e gravar arquivos no recurso de armazenamento local.

As seções relevantes do arquivo de @ **Definition. csdef** são mostradas aqui:

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

Por exemplo, esse arquivo em lotes **Startup. cmd** usa a variável de ambiente **PathToStartupStorage** para criar o arquivo **myTest. txt** no local de armazenamento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Você pode acessar a pasta de armazenamento local do SDK do Azure usando o método [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Executar no emulador ou na nuvem
Você pode fazer com que sua tarefa de inicialização execute etapas diferentes quando estiver operando na nuvem em comparação com quando estiver no emulador de computação. Por exemplo, talvez você queira usar uma cópia nova de seus dados SQL somente quando estiver executando no emulador. Ou talvez você queira fazer algumas otimizações de desempenho para a nuvem que você não precisa fazer ao executar no emulador.

Essa capacidade de executar ações diferentes no emulador de computação e na nuvem pode ser realizada criando uma variável de ambiente no arquivo de [ServiceDefinition.csdef] . Em seguida, você testa essa variável de ambiente para um valor em sua tarefa de inicialização.

Para criar a variável de ambiente, adicione o elemento [Variable]/[RoleInstanceValue] e crie um valor XPath `/RoleEnvironment/Deployment/@emulated`de. O valor da variável de ambiente **% ComputeEmulatorRunning%** é `true` quando executado no emulador de computação e `false` quando executado na nuvem.

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

A tarefa agora pode verificar a variável de ambiente **% ComputeEmulatorRunning%** para executar ações diferentes com base em se a função está sendo executada na nuvem ou no emulador. Aqui está um script de Shell. cmd que verifica essa variável de ambiente.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detectar que a tarefa já foi executada
A função pode ser reciclada sem uma reinicialização, fazendo com que as tarefas de inicialização sejam executadas novamente. Não há nenhum sinalizador para indicar que uma tarefa já foi executada na VM de hospedagem. Você pode ter algumas tarefas onde não importa que elas sejam executadas várias vezes. No entanto, você pode encontrar uma situação em que você precisa impedir que uma tarefa seja executada mais de uma vez.

A maneira mais simples de detectar que uma tarefa já foi executada é criar um arquivo na pasta **% Temp%** quando a tarefa for bem-sucedida e procurar no início da tarefa. Aqui está um exemplo de script de shell cmd que faz isso para você.

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

## <a name="task-best-practices"></a>Práticas recomendadas de tarefa
Aqui estão algumas práticas recomendadas que você deve seguir ao configurar a tarefa para sua função Web ou de trabalho.

### <a name="always-log-startup-activities"></a>Sempre registrar atividades de inicialização
O Visual Studio não fornece um depurador para percorrer arquivos em lotes, portanto, é bom obter o máximo possível de dados na operação de arquivos em lotes. Registrar em log a saída de arquivos em lotes, **stdout** e **stderr**, pode fornecer informações importantes ao tentar depurar e corrigir arquivos em lotes. Para registrar **stdout** e **stderr** no arquivo StartupLog. txt no diretório apontado pela variável de ambiente **% Temp%** , adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao final das linhas específicas que você deseja registrar. Por exemplo, para executar Setup. exe no diretório **% PathToApp1Install%** :

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Para simplificar o XML, você pode criar um arquivo *cmd* do wrapper que chama todas as tarefas de inicialização juntamente com o registro em log e garante que cada tarefa filho Compartilhe as mesmas variáveis de ambiente.

Talvez você ache desagradável usar `>> "%TEMP%\StartupLog.txt" 2>&1` no final de cada tarefa de inicialização. Você pode impor o log de tarefas criando um wrapper que manipula o log para você. Esse wrapper chama o arquivo em lotes real que você deseja executar. Qualquer saída do arquivo em lotes de destino será redirecionada para o arquivo *Startuplog. txt* .

O exemplo a seguir mostra como redirecionar todas as saídas de um arquivo em lotes de inicialização. Neste exemplo, o arquivo arquivo serverdefinition. csdef cria uma tarefa de inicialização que chama *logwrap. cmd*. *logwrap. cmd* chama *Startup2. cmd*, redirecionando todas as saídas para **% temp\\% StartupLog. txt**.

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

Exemplo de saída no arquivo **StartupLog. txt** :

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O arquivo **StartupLog. txt** está localizado na pasta *C:\Resources\temp\\{identificador de função} \RoleTemp*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir o executionContext adequadamente para tarefas de inicialização
Defina os privilégios adequadamente para a tarefa de inicialização. Às vezes, as tarefas de inicialização devem ser executadas com privilégios elevados, embora a função seja executada com privilégios normais.

O atributo de[tarefa] [ExecutionContext]define o nível de privilégio da tarefa de inicialização. O `executionContext="limited"` uso de significa que a tarefa de inicialização tem o mesmo nível de privilégio que a função. O `executionContext="elevated"` uso de significa que a tarefa de inicialização tem privilégios de administrador, o que permite que a tarefa de inicialização Execute tarefas de administrador sem conceder privilégios de administrador à sua função.

Um exemplo de uma tarefa de inicialização que requer privilégios elevados é uma tarefa de inicialização que usa **Appcmd. exe** para configurar o IIS. **Appcmd. exe** requer `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Usar o taskType apropriado
O atributo de[tarefa] [TaskType]determina a maneira como a tarefa de inicialização é executada. Há três valores: **simples**, **plano de fundo**e **primeiro plano**. As tarefas de segundo plano e primeiro plano são iniciadas de forma assíncrona e, em seguida, as tarefas simples são executadas de maneira síncrona, uma de cada vez.

Com tarefas de inicialização **simples** , você pode definir a ordem na qual as tarefas são executadas pela ordem em que as tarefas são listadas no arquivo de imdefinição. csdef. Se uma tarefa **simples** terminar com um código de saída diferente de zero, o procedimento de inicialização será interrompido e a função não será iniciada.

A diferença entre tarefas de inicialização **em segundo plano** e tarefas de inicialização em **primeiro** plano é que as tarefas em **primeiro plano** mantêm a função em execução até o término da tarefa em **primeiro plano** Isso também significa que, se a tarefa em **primeiro plano** parar ou falhar, a função não será reciclada até que a tarefa em **primeiro plano** seja fechada de forma forçada. Por esse motivo, as tarefas **em segundo plano** são recomendadas para tarefas de inicialização assíncronas, a menos que você precise desse recurso da tarefa em **primeiro plano** .

### <a name="end-batch-files-with-exit-b-0"></a>Encerrar arquivos em lotes com EXIT/B 0
A função só será iniciada se o **ERRORLEVEL** de cada uma das tarefas de inicialização simples for zero. Nem todos os programas definem o **ERRORLEVEL** (código de saída) corretamente, portanto, o arquivo em `EXIT /B 0` lotes deve terminar com um se tudo tiver sido executado corretamente.

Um ausente `EXIT /B 0` no final de um arquivo em lotes de inicialização é uma causa comum de funções que não iniciam.

> [!NOTE]
> Observei que arquivos em lotes aninhados às vezes travam `/B` ao usar o parâmetro. Talvez você queira ter certeza de que esse problema não ocorrerá se outro arquivo em lotes chamar o arquivo em lotes atual, como se você usa o [wrapper de log](#always-log-startup-activities). Você pode omitir `/B` o parâmetro nesse caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Esperar que as tarefas de inicialização sejam executadas mais de uma vez
Nem todas as reciclagens de função incluem uma reinicialização, mas todas as reciclagens de função incluem a execução de todas as tarefas de inicialização. Isso significa que as tarefas de inicialização devem ser capazes de executar várias vezes entre as reinicializações sem problemas. Isso é discutido na [seção anterior](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Use o armazenamento local para armazenar arquivos que devem ser acessados na função
Se você quiser copiar ou criar um arquivo durante a tarefa de inicialização que, em seguida, seja acessível à sua função, esse arquivo deverá ser colocado no armazenamento local. Consulte a [seção anterior](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Passos seguintes
Examinar o [modelo e o pacote do serviço](cloud-services-model-and-package.md) de nuvem

Saiba mais sobre como [as tarefas](cloud-services-startup-tasks.md) funcionam.

[Crie e implante](cloud-services-how-to-create-deploy-portal.md) seu pacote de serviço de nuvem.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Extremidade]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
