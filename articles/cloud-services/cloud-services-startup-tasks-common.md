---
title: Tarefas de arranque comuns para serviços em nuvem | Documentos da Microsoft
description: Fornece alguns exemplos de tarefas de arranque comuns, que pode querer executar na sua função de web de serviços cloud ou a função de trabalho.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: ec3952f2bb0b4180f5c72d948d1835a903152f0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181831"
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas de arranque do serviço Cloud comuns
Este artigo fornece alguns exemplos de tarefas de arranque comuns, que talvez queira executar no seu serviço cloud. Pode utilizar tarefas de arranque para executar operações antes de uma função de inicialização. As operações que pode querer executar incluem instalar um componente, registar componentes COM, definir chaves do Registro ou a partir de um processo de execução demorada. 

Ver [este artigo](cloud-services-startup-tasks.md) para compreender como funcionam as tarefas de arranque e, especificamente, como criar as entradas que definem uma tarefa de arranque.

> [!NOTE]
> Tarefas de arranque não são aplicáveis a máquinas virtuais, apenas a Web do serviço de nuvem e funções de trabalho.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definir variáveis de ambiente antes de uma função de inicialização
Se precisar de variáveis de ambiente definidas para uma tarefa específica, utilize o [ambiente] elemento dentro do [tarefa] elemento.

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

As variáveis também podem utilizar um [platná hodnota XPath de Azure](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implementação. Em vez de utilizar o `value` atributo, definir um [RoleInstanceValue] elemento subordinado.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configurar arranque do IIS com AppCmd.exe
O [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) ferramenta da linha de comandos pode ser utilizada para gerir as definições de IIS durante o arranque no Azure. *AppCmd.exe* fornece acesso conveniente e da linha de comandos para as definições de configuração para uso em tarefas de arranque no Azure. Usando *AppCmd.exe*, definições de Web site podem ser adicionadas, modificadas ou removidas para aplicativos e sites.

No entanto, existem algumas coisas a serem observadas na utilização de *AppCmd.exe* como uma tarefa de arranque:

* Tarefas de arranque podem ser executadas mais do que uma vez entre reinícios. Por exemplo, quando uma função é reciclado.
* Se um *AppCmd.exe* ação é executada mais do que uma vez, pode gerar um erro. Por exemplo, a tentar adicionar uma seção para *Web. config* duas vezes, pode gerar um erro.
* Tarefas de arranque falharem se de que elas retornam um código de saída diferente de zero ou **errorlevel**. Por exemplo, quando *AppCmd.exe* gera um erro.

É uma boa prática para verificar a **errorlevel** após chamar *AppCmd.exe*, que é fácil fazê-lo se encapsular a chamada para *AppCmd.exe* com um *. cmd* ficheiro. Se detectar um conhecido **errorlevel** resposta, pode ignorá-la ou transferi-los de volta.

O nível de erro devolvido pelo *AppCmd.exe* estão listados no ficheiro winerror.h e também pode ser visto no [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gestão do nível de erro
Este exemplo adiciona uma seção de compactação e uma entrada de compressão para JSON para o *Web. config* arquivo, com o erro de processamento e o registo.

As seções relevantes da [ServiceDefinition.csdef] arquivo são mostrados aqui, que incluem a definição a [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) atributo para `elevated` dar *AppCmd.exe* permissões suficientes para alterar as definições no *Web. config* ficheiro:

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

O *Startup.cmd* utiliza ficheiros do batch *AppCmd.exe* para adicionar uma secção de compactação e uma entrada de compressão para JSON para o *Web. config* ficheiro. A esperada **errorlevel** da 183 é definido como zero usando a verificar. Programa de linha de comando EXE. São registados StartupErrorLog.txt errorlevels inesperados.

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
No Azure, existem dois firewalls de forma eficaz. A primeira firewall controla ligações entre a máquina virtual e o mundo exterior. Esta firewall é controlado pelos [Pontos finais] elemento no [ServiceDefinition.csdef] ficheiro.

O segundo firewall controla ligações entre a máquina virtual e os processos dentro do que a máquina virtual. Esta firewall pode ser controlado pelo `netsh advfirewall firewall` ferramenta da linha de comandos.

O Azure cria regras de firewall para os processos iniciados dentro de suas funções. Por exemplo, quando inicia um serviço ou programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que esse serviço comunicar com a Internet. No entanto, se criar um serviço que é iniciado por um processo fora da sua função (como um serviço de COM+ ou uma tarefa agendada do Windows), terá de criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Estas regras de firewall podem ser criadas utilizando uma tarefa de arranque.

Uma tarefa de arranque que cria uma regra de firewall tem de ter uma [executionContext][tarefa] de **elevados**. Adicione a seguinte tarefa de arranque para o [ServiceDefinition.csdef] ficheiro.

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

Para adicionar a regra de firewall, tem de utilizar o adequado `netsh advfirewall firewall` comandos em seu arquivo de lote de arranque. Neste exemplo, a tarefa de arranque exige segurança e encriptação para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloquear um endereço IP específico
Pode restringir um acesso de função da web do Azure a um conjunto de endereços IP especificados pela modificação do IIS **Web. config** ficheiro. Também tem de utilizar um ficheiro de comandos que desbloqueia a **ipSecurity** secção a **applicationHost. config** ficheiro.

Para desbloquear o **ipSecurity** secção a **applicationHost. config** de ficheiros, criar um arquivo de comando que é executado no início da função. Crie uma pasta no nível de raiz da sua função da web chamado **inicialização** e, dentro dessa pasta, criar um arquivo em lotes chamado **startup.cmd**. Adicionar este ficheiro ao seu projeto do Visual Studio e defina as propriedades **Copiar sempre** para se certificar de que está incluído no pacote.

Adicione a seguinte tarefa de arranque para o [ServiceDefinition.csdef] ficheiro.

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

Adicionar este comando para o **startup.cmd** ficheiro:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Esta tarefa faz com que o **startup.cmd** arquivo em lotes para ser executado sempre que a função da web é inicializada, garantindo que o necessário **ipSecurity** secção está desbloqueada.

Por fim, modifique o [secção System. webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a função da web **Web. config** ficheiro para adicionar uma lista de endereços IP que é concedido acesso, conforme mostrado no exemplo a seguir:

Esta configuração de exemplo **permite** todos os IPs para aceder ao servidor, exceto as duas definidas

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

Esta configuração de exemplo **nega** todos os IPs acedam ao servidor exceto para as duas definidas.

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

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de arranque do PowerShell
Scripts do Windows PowerShell não podem ser chamados diretamente a partir da [ServiceDefinition.csdef] ficheiro, mas eles podem ser invocados dentro de um arquivo de lote de arranque.

PowerShell (por predefinição) não executa scripts não assinados. A menos que se o seu script, terá de configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, o **ExecutionPolicy** tem de ser definido como **irrestrito**. O **ExecutionPolicy** definição que utilize se baseia na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se estiver a utilizar o SO convidado que é executado o PowerShell 2.0 ou 1.0, que pode forçar a versão 2 para executar e, se disponível, utilize a versão 1.

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
Pode usar um recurso de armazenamento local para armazenar os ficheiros criados pela sua tarefa de arranque que é acessada mais tarde pelo seu aplicativo.

Para criar o recurso de armazenamento local, adicione uma [LocalResources] secção para o [ServiceDefinition.csdef] de ficheiros e, em seguida, adicione o [LocalStorage] elemento subordinado. Atribua o recurso de armazenamento local para um nome exclusivo e um tamanho adequado para a sua tarefa de arranque.

Para utilizar um recurso de armazenamento local na sua tarefa de arranque, terá de criar uma variável de ambiente para fazer referência a localização de recursos de armazenamento local. Em seguida, a tarefa de arranque e a aplicação são capazes de ler e escrever ficheiros para o recurso de armazenamento local.

As seções relevantes da **servicedefinition. Csdef** arquivo são mostrados aqui:

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

Por exemplo, isso **Startup.cmd** utiliza ficheiros do batch a **PathToStartupStorage** variável de ambiente para criar o ficheiro **MyTest.txt** na localização de armazenamento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Pode aceder a pasta de armazenamento local do SDK do Azure ao utilizar o [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) método.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Executar no emulador ou na cloud
Pode ter sua tarefa de arranque realizar passos diferentes quando está a funcionar na cloud que se está a ser o emulador de computação. Por exemplo, pode querer utilizar uma cópia nova dos seus dados SQL apenas durante a execução no emulador. Ou talvez queira fazer algumas otimizações de desempenho para a nuvem que não precisa de fazer quando em execução no emulador.

Esta capacidade para efetuar ações diferentes no emulador de computação e a cloud pode ser conseguida através da criação de uma variável de ambiente no [ServiceDefinition.csdef] ficheiro. Em seguida, testar essa variável de ambiente para um valor na sua tarefa de arranque.

Para criar a variável de ambiente, adicione a [Variable]/[RoleInstanceValue] elemento e criar um valor de XPath de `/RoleEnvironment/Deployment/@emulated`. O valor do **% ComputeEmulatorRunning %** variável de ambiente está `true` quando em execução no emulador de computação, e `false` quando em execução na cloud.

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

A tarefa agora pode verificar o **% ComputeEmulatorRunning %** variável de ambiente para executar ações diferentes com base em se a função está em execução na cloud ou no emulador. Eis um script de shell. cmd que verifica a existência dessa variável de ambiente.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detetar se a sua tarefa já foi executada
A função pode reciclar sem uma reinicialização, fazendo com que as tarefas de arranque executar novamente. Não existe nenhum sinalizador para indicar se uma tarefa já foi executada na VM do alojamento. Pode ter algumas tarefas em que não importa que eles são executados de várias vezes. No entanto, que poderão ocorrer numa situação em que precisa para impedir que uma tarefa em execução mais do que uma vez.

A forma mais simples para detetar se uma tarefa já foi executada é criar um ficheiro no **% TEMP %** pasta quando a tarefa for concluída com êxito e procure-lo no início da tarefa. Eis um script de shell cmd de exemplo que faz isso para.

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

## <a name="task-best-practices"></a>Melhores práticas de tarefas
Aqui estão algumas práticas recomendadas que deve seguir ao configurar a tarefa para a sua função web ou de trabalho.

### <a name="always-log-startup-activities"></a>Sempre atividades de arranque do registo
Visual Studio não fornece um depurador para examinar a arquivos em lote, portanto, é bom obter a quantidade de dados sobre a operação de arquivos em lote possível. Registo a saída de arquivos em lote, ambos **stdout** e **stderr**, pode fornecer informações importantes ao tentar depurar e corrigir arquivos em lote. Para iniciar sessão ambos **stdout** e **stderr** para o StartupLog.txt ficheiro no diretório apontada pelos **% TEMP %** variável de ambiente, adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao final do linhas específicas para registrar em log. Por exemplo, para executar setup.exe no **% PathToApp1Install %** diretório:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Para simplificar seu xml, pode criar um wrapper *cmd* tarefas, juntamente com o registo de ficheiros que chama todos sua startup e garante que cada tarefa filho compartilha as mesmas variáveis de ambiente.

Pode ser irritante para utilizar `>> "%TEMP%\StartupLog.txt" 2>&1` no final de cada tarefa de arranque. Pode impor o registo de tarefas através da criação de um wrapper que processa o registo para. Esse wrapper chama o arquivo em lotes real que pretende executar. Qualquer saída de arquivo em lotes o destino será redirecionada para o *Startuplog.txt* ficheiro.

O exemplo seguinte mostra como redirecionar toda a saída de um ficheiro de batch de arranque. Neste exemplo, ficheiro ServerDefinition.csdef cria uma tarefa de arranque que chama *logwrap.cmd*. *logwrap.cmd* chamadas *Startup2.cmd*, toda a saída para a redirecionar **% TEMP %\\StartupLog.txt**.

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

Exemplo de saída no **StartupLog.txt** ficheiro:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O **StartupLog.txt** ficheiro está localizado na *C:\Resources\temp\\{identificador de função} \RoleTemp* pasta.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir o executionContext adequadamente para tarefas de arranque
Definido privilégios adequadamente para a tarefa de arranque. Por vezes, as tarefas de arranque tem de executar com privilégios elevados, apesar da função é executado com privilégios normais.

O [executionContext][tarefa] atributo define o nível de privilégio da tarefa de arranque. Usando `executionContext="limited"` significa que a tarefa de arranque tem o mesmo nível de privilégio que a função. Usando `executionContext="elevated"` significa que a tarefa de arranque tem privilégios de administrador, que permite que a tarefa de arranque executar tarefas de administrador sem fornecer privilégios de administrador à sua função.

Um exemplo de uma tarefa de arranque que exige privilégios elevados é uma tarefa de arranque que utiliza **AppCmd.exe** para configurar o IIS. **AppCmd.exe** requer `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Utilizar o taskType apropriado
O [taskType][tarefa] atributo determina a forma como a tarefa de arranque é executado. Há três valores: **simples**, **em segundo plano**, e **em primeiro plano**. As tarefas de background e foreground são iniciadas de forma assíncrona e, em seguida, as tarefas simples são executadas de forma síncrona um de cada vez.

Com o **simples** tarefas de arranque, pode definir a ordem na qual as tarefas são executadas pela ordem em que as tarefas estão listadas no ficheiro Servicedefinition csdef. Se um **simples** tarefa termina com um código de saída diferente de zero, em seguida, o pára de procedimento de arranque e a função não for iniciado.

A diferença entre **em segundo plano** tarefas de arranque e **primeiro plano** é que, tarefas de arranque **em primeiro plano** tarefas manter a função em execução até a  **em primeiro plano** extremidades de tarefas. Isso também significa que, se o **em primeiro plano** pára de tarefas ou falhas, a função não reciclará até a **primeiro plano** é forçada a tarefa fechado. Por esse motivo, **em segundo plano** tarefas são recomendadas para tarefas de inicialização assíncrona, a menos que precise essa funcionalidade dos **primeiro plano** tarefas.

### <a name="end-batch-files-with-exit-b-0"></a>Arquivos em lote final com a/b de saída 0
A função será iniciada apenas se o **errorlevel** de cada um dos sua startup simple a tarefa for igual a zero. Nem todos os programas definir os **errorlevel** (código de saída) corretamente, portanto, o arquivo em lote deve terminar com um `EXIT /B 0` se tudo o que foi executada corretamente.

Falta uma `EXIT /B 0` no final de um lote de inicialização o ficheiro é uma causa comum das funções que não se iniciam.

> [!NOTE]
> Observei que batch aninhada ficheiros parada, às vezes, ao utilizar o `/B` parâmetro. Talvez queira Certifique-se de que esse problema de travamento não ocorre se outro arquivo em lote chama seu arquivo em lotes atual, como se utilizar o [wrapper de log](#always-log-startup-activities). Pode omitir o `/B` parâmetro neste caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Tarefas de arranque para executar mais de uma vez de esperar
Nem todos os reciclagem da função incluem uma reinicialização, mas todos os reciclagem da função incluem todas as tarefas de arranque em execução. Isso significa que as tarefas de arranque tem de ser capazes de executar várias vezes entre reinícios sem problemas. Isso é discutido com o [secção anterior](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Utilize o armazenamento local para armazenar os ficheiros que devem ser acessados na função
Se desejar copiar ou crie um ficheiro durante a sua tarefa de arranque, em seguida, está acessível para a sua função, em seguida, esse arquivo deve ser colocado no armazenamento local. Consulte a [secção anterior](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Passos Seguintes
Reveja a cloud [modelo e um pacote de serviço](cloud-services-model-and-package.md)

Saiba mais sobre como [tarefas](cloud-services-startup-tasks.md) trabalhar.

[Criar e implementar](cloud-services-how-to-create-deploy-portal.md) seu pacote de serviço cloud.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Pontos finais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
