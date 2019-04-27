---
title: Executar tarefas de arranque nos serviços Cloud do Azure | Documentos da Microsoft
description: Tarefas de arranque ajudam a preparar o ambiente de serviço em nuvem para a sua aplicação. Isso informa-o como funcionam as tarefas de arranque e como criá-los
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 59bfa83ab3432adb7a4df5112367f87014a0b292
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405992"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de arranque para um serviço cloud
Pode utilizar tarefas de arranque para executar operações antes de uma função de inicialização. As operações que pode querer executar incluem instalar um componente, registar componentes COM, definir chaves do Registro ou a partir de um processo de execução demorada.

> [!NOTE]
> Tarefas de arranque não são aplicáveis a máquinas virtuais, apenas a Web do serviço de nuvem e funções de trabalho.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de arranque
Tarefas de arranque são ações que são executadas antes das funções começarem e são definidas no [ServiceDefinition.csdef] ficheiro utilizando o [tarefa] elemento dentro do [arranque] elemento. Com frequência as tarefas de arranque são arquivos em lote, mas eles também podem ser aplicativos de console ou arquivos em lote que começam de scripts do PowerShell.

Variáveis de ambiente passam informações para uma tarefa de arranque e armazenamento local pode ser usado para passar informações fora de uma tarefa de arranque. Por exemplo, uma variável de ambiente, pode especificar o caminho para um programa que pretende instalar, e pode escrever nos ficheiros para o armazenamento local que, em seguida, pode ser lido mais tarde por suas funções.

Sua tarefa de arranque, pode iniciar informações e erros para o diretório especificado pela **TEMP** variável de ambiente. Durante a tarefa de arranque, o **TEMP** variável de ambiente é resolvido para o *c:\\recursos\\temp\\[guid]. [ rolename]\\RoleTemp* diretório quando em execução na cloud.

As tarefas de arranque também podem ser executadas várias vezes entre reinícios. Por exemplo, a tarefa de arranque será executada sempre que a função reciclar e a reciclagem da função pode não incluir sempre um reinício. Tarefas de arranque devem ser escritas de forma que permita que seja executado várias vezes sem problemas.

Tarefas de arranque tem de terminar com uma **errorlevel** (ou código de saída) de zero para o conclusão do processo de inicialização. Se uma tarefa de arranque terminar com um diferente de zero **errorlevel**, a função não será iniciado.

## <a name="role-startup-order"></a>Sequência de arranque de função
A seguir listam-se o procedimento de arranque de função no Azure:

1. A instância é marcada como **inicial** e não receber o tráfego.
2. Todas as tarefas de arranque são executadas de acordo com seus **taskType** atributo.
   
   * O **simples** tarefas são executadas de forma síncrona, uma de cada vez.
   * O **em segundo plano** e **primeiro plano** tarefas são iniciadas de forma assíncrona, em paralelo para a tarefa de arranque.  
     
     > [!WARNING]
     > IIS pode não estar totalmente configurado durante a fase de tarefa de arranque no processo de inicialização, para que os dados específicos da função poderão não estar disponíveis. Devem utilizar tarefas de arranque que dados de função específicas [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. O processo de função de anfitrião é iniciado e o site for criado no IIS.
4. O [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) método é chamado.
5. A instância é marcada como **pronto** e o tráfego é encaminhado para a instância.
6. O [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) método é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de arranque
Tarefas de arranque são definidas na [ServiceDefinition.csdef] , além de ficheiros a **tarefa** elemento. O **commandLine** atributo especifica o nome e os parâmetros do arranque batch ficheiro ou a consola de comando, o **executionContext** atributo especifica o nível de privilégio da tarefa de arranque e o **taskType** atributo especifica como a tarefa será executada.

Neste exemplo, uma variável de ambiente **MyVersionNumber**, é criada para a tarefa de arranque e definido para o valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo a seguir, o **Startup.cmd** ficheiro batch escreve a linha "a versão atual é 1.0.0.0" para o ficheiro de StartupLog.txt no diretório especificado pela variável de ambiente TEMP. O `EXIT /B 0` linha garante que a tarefa de arranque termina com um **errorlevel** igual a zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, o **Copy to Output Directory** propriedade para o ficheiro de batch de arranque deve ser definida como **cópia sempre** para ter certeza de que seu arquivo de lote de arranque está corretamente implementado ao seu projeto no Azure (**approot\\bin** para funções da Web, e **approot** para funções de trabalho).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição de atributos de tarefas
O seguinte descreve os atributos do **tarefa** elemento no [ServiceDefinition.csdef] ficheiro:

**linha de comandos** -Especifica a linha de comandos para a tarefa de arranque:

* O comando, com parâmetros de linha de comandos opcionais, que começa a tarefa de arranque.
* Freqüentemente, isso é o nome de ficheiro de um arquivo em lotes. cmd ou. bat.
* A tarefa é relativo a AppRoot\\pasta Bin para a implementação. Variáveis de ambiente não são expandidas determinar o caminho e o ficheiro da tarefa. Se for necessária a expansão de ambiente, pode criar um script. cmd pequeno que chama a sua tarefa de arranque.
* Pode ser um aplicativo de console ou um arquivo em lotes que inicia uma [script do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** -Especifica o nível de privilégio para a tarefa de arranque. O nível de privilégio pode ser limitado ou elevado:

* **limited**  
  A tarefa de arranque é executado com os mesmos privilégios que a função. Quando o **executionContext** atributo para o [tempo de execução] elemento é também **limitado**, privilégios de usuário serão utilizadas.
* **elevated**  
  A tarefa de arranque é executado com privilégios de administrador. Isso permite que as tarefas de arranque para instalar programas, fazer alterações de configuração do IIS, efetuar as alterações de registo e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da função propriamente dito.  

> [!NOTE]
> O nível de privilégio de uma tarefa de arranque não precisa de ser o mesmo que a função em si.
> 
> 

**taskType** -Especifica a forma como uma tarefa de arranque é executada.

* **simple**  
  Tarefas são executadas de forma síncrona, uma de cada vez, na ordem especificada na [ServiceDefinition.csdef] ficheiro. Quando um **simples** tarefa de arranque terminar com um **errorlevel** igual a zero, a próxima **simples** é executada a tarefa de arranque. Se existirem mais **simples** tarefas de arranque para executar, em seguida, a função em si será iniciada.   
  
  > [!NOTE]
  > Se o **simples** tarefa termina com um diferente de zero **errorlevel**, a instância será bloqueada. Subsequentes **simples** tarefas de arranque e a função em si, não serão iniciado.
  > 
  > 
  
    Para garantir que seu arquivo em lotes termina com uma **errorlevel** de zero, execute o comando `EXIT /B 0` no final do seu processo de ficheiro de batch.
* **background**  
  Tarefas são executadas de forma assíncrona, em paralelo com o arranque da função.
* **foreground**  
  Tarefas são executadas de forma assíncrona, em paralelo com o arranque da função. A principal diferença entre uma **em primeiro plano** e uma **em segundo plano** tarefa é que uma **em primeiro plano** tarefas impede que a função de reciclagem ou encerrar até que a tarefa terminou. O **em segundo plano** tarefas não têm essa restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
Variáveis de ambiente são uma forma para passar informações para uma tarefa de arranque. Por exemplo, pode colocar o caminho para um blob que contém um programa para instalar, ou os números de porta que irá utilizar a sua função ou as definições para controlar as funcionalidades de sua tarefa de arranque.

Existem dois tipos de variáveis de ambiente para tarefas de arranque variáveis de ambiente estático e variáveis de ambiente com base em membros do [RoleEnvironment] classe. Ambos estão no [ambiente] secção a [ServiceDefinition.csdef] arquivo e tanto uso o [Variable] elemento e **nome** atributo.

Variáveis de ambiente estáticos utiliza a **valor** atributo da [Variable] elemento. O exemplo acima cria a variável de ambiente **MyVersionNumber** que tem o valor estático "**1.0.0.0**". Outro exemplo seria criar um **StagingOrProduction** variável de ambiente que pode definir manualmente para valores de "**de teste**"ou"**produção**" para executar ações de arranque diferentes com base no valor do **StagingOrProduction** variável de ambiente.

Variáveis de ambiente com base nos membros da classe RoleEnvironment não utilize o **valor** atributo da [Variable] elemento. Em vez disso, o [RoleInstanceValue] elemento subordinado, com o adequado **XPath** valor de atributo, são utilizados para criar uma variável de ambiente com base num membro específico do [RoleEnvironment] classe. Os valores para o **XPath** atributo para aceder a várias [RoleEnvironment] valores podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável de ambiente que é "**true**" quando a instância está em execução no emulador de computação, e "**false**" quando em execução na cloud, utilize o seguinte [Variable] e [RoleInstanceValue] elementos:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Passos Seguintes
Saiba como executar algumas [tarefas de arranque comuns](cloud-services-startup-tasks-common.md) com o seu serviço Cloud.

[Pacote](cloud-services-model-and-package.md) seu serviço Cloud.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Arranque]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Tempo de execução]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
