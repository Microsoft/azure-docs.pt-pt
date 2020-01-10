---
title: Executar tarefas de inicialização nos serviços de nuvem do Azure | Microsoft Docs
description: As tarefas de inicialização ajudam a preparar seu ambiente de serviço de nuvem para seu aplicativo. Isso ensina como as tarefas de inicialização funcionam e como torná-las
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360315"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de inicialização para um serviço de nuvem
Você pode usar tarefas de inicialização para executar operações antes de uma função ser iniciada. As operações que você pode querer executar incluem a instalação de um componente, o registro de componentes COM, a definição de chaves do registro ou o início de um processo de execução demorada.

> [!NOTE]
> As tarefas de inicialização não são aplicáveis às máquinas virtuais, somente às funções Web e de trabalho do serviço de nuvem.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de inicialização
As tarefas de inicialização são ações que são executadas antes de suas funções começarem e são definidas no arquivo de [ServiceDefinition.csdef] usando o elemento [Tarefa] dentro do elemento [Startup] . As tarefas de inicialização frequentes são arquivos em lotes, mas também podem ser aplicativos de console ou arquivos em lotes que iniciam scripts do PowerShell.

As variáveis de ambiente passam informações para uma tarefa de inicialização e o armazenamento local pode ser usado para passar informações de uma tarefa de inicialização. Por exemplo, uma variável de ambiente pode especificar o caminho para um programa que você deseja instalar, e os arquivos podem ser gravados no armazenamento local que, em seguida, podem ser lidos posteriormente por suas funções.

A tarefa de inicialização pode registrar informações e erros no diretório especificado pela variável de ambiente **Temp** . Durante a tarefa de inicialização, a variável de ambiente **Temp** é resolvida para os *recursos C:\\\\Temp\\[GUID]. [ roleName]\\diretório RoleTemp* ao executar na nuvem.

As tarefas de arranque também podem ser executadas várias vezes entre reinícios. Por exemplo, a tarefa de arranque será executada sempre que a função reciclar e a reciclagem da função pode não incluir sempre um reinício. As tarefas de inicialização devem ser escritas de forma a permitir que elas sejam executadas várias vezes sem problemas.

As tarefas de inicialização devem terminar com um **ERRORLEVEL** (ou código de saída) de zero para que o processo de inicialização seja concluído. Se uma tarefa de inicialização terminar com um **ERRORLEVEL**diferente de zero, a função não será iniciada.

## <a name="role-startup-order"></a>Ordem de inicialização de função
O seguinte lista o procedimento de inicialização de função no Azure:

1. A instância é marcada como **iniciando** e não recebe tráfego.
2. Todas as tarefas de inicialização são executadas de acordo com o atributo **TaskType** .
   
   * As tarefas **simples** são executadas de forma síncrona, uma de cada vez.
   * As tarefas **em segundo plano e em** **primeiro plano** são iniciadas de forma assíncrona, paralelamente à tarefa de inicialização.  
     
     > [!WARNING]
     > O IIS pode não estar totalmente configurado durante o estágio de tarefa de inicialização no processo de inicialização, portanto, os dados específicos de função podem não estar disponíveis. As tarefas de inicialização que exigem dados específicos de função devem usar [Microsoft. WindowsAzure. inruntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. O processo de host da função é iniciado e o site é criado no IIS.
4. O método [Microsoft. WindowsAzure. inruntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) é chamado.
5. A instância é marcada como **pronta** e o tráfego é roteado para a instância.
6. O método [Microsoft. WindowsAzure. netruntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de inicialização
As tarefas de inicialização são definidas no arquivo de [ServiceDefinition.csdef] , no elemento **Task** . O atributo **CommandLine** especifica o nome e os parâmetros do arquivo em lotes de inicialização ou do comando do console, o atributo **ExecutionContext** especifica o nível de privilégio da tarefa de inicialização e o atributo **TaskType** especifica como a tarefa será executada.

Neste exemplo, uma variável de ambiente, **myversionnumber**, é criada para a tarefa de inicialização e é definida com o valor "**1.0.0.0**".

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

No exemplo a seguir, o arquivo em lotes **Startup. cmd** grava a linha "a versão atual é 1.0.0.0" para o arquivo StartupLog. txt no diretório especificado pela variável de ambiente TEMP. A linha de `EXIT /B 0` garante que a tarefa de inicialização termine com um **ERRORLEVEL** igual a zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, a propriedade **copiar para o diretório de saída** para o arquivo em lotes de inicialização deve ser definida como **copiar sempre** para ter certeza de que o arquivo em lotes de inicialização está implantado corretamente em seu projeto no Azure (**approot\\bin** para funções Web e **approot** para funções de trabalho).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição dos atributos da tarefa
O seguinte descreve os atributos do elemento **Task** no arquivo de [ServiceDefinition.csdef] :

**CommandLine** – especifica a linha de comando para a tarefa de inicialização:

* O comando, com parâmetros de linha de comando opcionais, que inicia a tarefa de inicialização.
* Frequentemente, esse é o nome do arquivo em lotes. cmd ou. bat.
* A tarefa é relativa à pasta AppRoot\\bin para a implantação. As variáveis de ambiente não são expandidas para determinar o caminho e o arquivo da tarefa. Se a expansão do ambiente for necessária, você poderá criar um script Small. cmd que chama sua tarefa de inicialização.
* Pode ser um aplicativo de console ou um arquivo em lotes que inicia um [script do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**ExecutionContext** -especifica o nível de privilégio para a tarefa de inicialização. O nível de privilégio pode ser limitado ou elevado:

* **Certo**  
  A tarefa de inicialização é executada com os mesmos privilégios que a função. Quando o atributo **ExecutionContext** do elemento [Appmodel] também é **limitado**, os privilégios de usuário são usados.
* **com privilégios elevados**  
  A tarefa de inicialização é executada com privilégios de administrador. Isso permite que as tarefas de inicialização instalem programas, façam alterações na configuração do IIS, executem alterações no registro e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da própria função.  

> [!NOTE]
> O nível de privilégio de uma tarefa de inicialização não precisa ser o mesmo que a própria função.
> 
> 

**TaskType** -especifica a maneira como uma tarefa de inicialização é executada.

* **simple**  
  As tarefas são executadas de forma síncrona, uma de cada vez, na ordem especificada no arquivo de [ServiceDefinition.csdef] . Quando uma tarefa de inicialização **simples** termina com um **ERRORLEVEL** igual a zero, a próxima tarefa de inicialização **simples** é executada. Se não houver nenhuma tarefa de inicialização mais **simples** para executar, a função em si será iniciada.   
  
  > [!NOTE]
  > Se a tarefa **simples** terminar com um **ERRORLEVEL**diferente de zero, a instância será bloqueada. As tarefas de inicialização **simples** subsequentes e a própria função não serão iniciadas.
  > 
  > 
  
    Para garantir que o arquivo em lotes termine com um **ERRORLEVEL** igual a zero, execute o comando `EXIT /B 0` no final do processo de arquivo em lotes.
* **background**  
  As tarefas são executadas de forma assíncrona, em paralelo com a inicialização da função.
* **foreground**  
  As tarefas são executadas de forma assíncrona, em paralelo com a inicialização da função. A principal diferença entre um **primeiro plano** e uma tarefa **em segundo plano** é que uma tarefa em **primeiro plano** impede que a função seja reciclada ou desligada até que a tarefa seja encerrada. As tarefas **em segundo plano** não têm essa restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
As variáveis de ambiente são uma maneira de passar informações para uma tarefa de inicialização. Por exemplo, você pode colocar o caminho para um blob que contém um programa a ser instalado ou os números de porta que sua função usará, ou configurações para controlar recursos de sua tarefa de inicialização.

Há dois tipos de variáveis de ambiente para tarefas de inicialização; variáveis de ambiente e variáveis de ambiente estáticas com base em membros da classe [RoleEnvironment] . Ambos estão na seção [ambiente] do arquivo de Service [ServiceDefinition.csdef] , e ambos usam o elemento [Variable] e o atributo **Name** .

As variáveis de ambiente estáticos usam o atributo **Value** do elemento [Variable] . O exemplo acima cria a variável de ambiente **Myversionnumber** , que tem um valor estático de "**1.0.0.0**". Outro exemplo seria criar uma variável de ambiente **StagingOrProduction** , que você pode definir manualmente para os valores de "**preparo**" ou "**produção**" para executar ações de inicialização diferentes com base no valor da variável de ambiente **StagingOrProduction** .

Variáveis de ambiente baseadas em membros da classe RoleEnvironment não usam o atributo **Value** do elemento [Variable] . Em vez disso, o elemento filho [RoleInstanceValue] , com o valor de atributo **XPath** apropriado, é usado para criar uma variável de ambiente com base em um membro específico da classe [RoleEnvironment] . Os valores para o atributo **XPath** para acessar vários valores de [RoleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável de ambiente que seja "**true**" quando a instância estiver em execução no emulador de computação e "**false**" durante a execução na nuvem, use os seguintes elementos [Variable] e [RoleInstanceValue] :

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

## <a name="next-steps"></a>Passos seguintes
Saiba como executar algumas [tarefas de inicialização comuns](cloud-services-startup-tasks-common.md) com seu serviço de nuvem.

[Empacote](cloud-services-model-and-package.md) seu serviço de nuvem.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Appmodel]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



