---
title: Executar tarefas de arranque em serviços de nuvem Azure [ Microsoft Docs
description: As tarefas de arranque ajudam a preparar o seu ambiente de serviço na nuvem para a sua aplicação. Isto ensina-lhe como as tarefas de arranque funcionam e como fazê-las
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75360315"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de arranque para um serviço na nuvem
Pode utilizar tarefas de arranque para realizar operações antes de iniciar uma função. As operações que desejare efetuar incluem a instalação de um componente, o registo de componentes COM, a definição de teclas de registo ou o início de um processo de longo prazo.

> [!NOTE]
> As tarefas de arranque não são aplicáveis às Máquinas Virtuais, apenas às funções Cloud Service Web e Worker.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de arranque
As tarefas de arranque são ações que são tomadas antes do início das suas funções e são definidas no ficheiro [ServiceDefinition.csdef] utilizando o elemento ['Tarefa'] dentro do elemento [Startup.] Frequentemente, as tarefas de arranque são ficheiros de lote, mas também podem ser aplicações de consola, ou ficheiros de lote que iniciam scripts PowerShell.

Variáveis ambientais passam informação para uma tarefa de arranque, e o armazenamento local pode ser usado para passar informação de uma tarefa de arranque. Por exemplo, uma variável ambiental pode especificar o caminho para um programa que pretende instalar, e os ficheiros podem ser escritos para armazenamento local que podem ser lidos mais tarde pelas suas funções.

A sua tarefa de arranque pode registar informações e erros no diretório especificado pela variável ambiente **TEMP.** Durante a tarefa de arranque, a variável ambiental **TEMP** resolve-se com a *temperatura\\C: Recursos\\\\[guia].. nome\\roleTemp* diretório ao correr na nuvem.

As tarefas de arranque também podem ser executadas várias vezes entre reinícios. Por exemplo, a tarefa de arranque será executada sempre que a função reciclar e a reciclagem da função pode não incluir sempre um reinício. As tarefas de arranque devem ser escritas de uma forma que lhes permita funcionar várias vezes sem problemas.

As tarefas de arranque devem terminar com um **nível** de erro (ou código de saída) de zero para que o processo de arranque esteja concluído. Se uma tarefa de arranque terminar com um **nível**de erro não zero, a função não será iniciada.

## <a name="role-startup-order"></a>Ordem de arranque de funções
O seguinte enumera o procedimento de arranque em Azure:

1. A ocorrência é marcada como **Starting** e não recebe tráfego.
2. Todas as tarefas de arranque são executadas de acordo com o seu atributo **taskType.**
   
   * As tarefas **simples** são executadas sincronizadamente, uma de cada vez.
   * As tarefas de **fundo** e **primeiro plano** são iniciadas de forma assíncrona, paralelaà tarefa de arranque.  
     
     > [!WARNING]
     > O IIS pode não estar totalmente configurado durante a fase de tarefa de arranque no processo de arranque, pelo que os dados específicos para as funções podem não estar disponíveis. As tarefas de arranque que requerem dados específicos de função devem utilizar [o Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. O processo de hospedar funções é iniciado e o site é criado no IIS.
4. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) é chamado.
5. A ocorrência é marcada como **Ready** e o tráfego é encaminhado para a instância.
6. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de arranque
As tarefas de arranque são definidas no ficheiro [ServiceDefinition.csdef,] no elemento **Tarefa.** O atributo **commandLine** especifica o nome e os parâmetros do ficheiro de lote de arranque ou comando de consola, o atributo **de execuçãoContext** especifica o nível de privilégio da tarefa de arranque, e o atributo **taskType** especifica como a tarefa será executada.

Neste exemplo, uma variável ambiental, **MyVersionNumber,** é criada para a tarefa de arranque e definida para o valor "**1.0.0.0**".

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

No exemplo seguinte, o ficheiro de lote **Startup.cmd** escreve a linha "A versão atual é 1.0.0.0" para o ficheiro StartupLog.txt no diretório especificado pela variável ambiente TEMP. A `EXIT /B 0` linha garante que a tarefa de arranque termina com um **nível** de erro de zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, a propriedade **Copy to Output Diretório** para o seu ficheiro de lote de arranque deve ser definida para **Copy Always** para ter a certeza de que o ficheiro de lote de arranque está devidamente implantado para o seu projeto no Azure**\\(approot bin** para funções Web e **approot** para funções de trabalhador).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição de atributos de tarefas
O seguinte descreve os atributos do elemento **Tarefa** no ficheiro [ServiceDefinition.csdef:]

**linha de comando** - Especifica a linha de comando para a tarefa de arranque:

* O comando, com parâmetros de linha de comando opcionais, que inicia a tarefa de arranque.
* Frequentemente este é o nome de ficheiro de um ficheiro de lote .cmd ou .bat.
* A tarefa é relativa\\à pasta AppRoot Bin para a implementação. As variáveis ambientais não são expandidas na determinação do caminho e do arquivo da tarefa. Se for necessária uma expansão ambiental, pode criar um pequeno script .cmd que chama a sua tarefa de arranque.
* Pode ser uma aplicação de consola ou um ficheiro de lote que inicia um [script PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**execuçãoContexto** - Especifica o nível de privilégio para a tarefa de arranque. O nível de privilégio pode ser limitado ou elevado:

* **limitado**  
  A tarefa de arranque funciona com os mesmos privilégios que o papel. Quando o atributo de contexto de **execução** para o elemento Tempo de [Execução] também é **limitado,** então os privilégios do utilizador são usados.
* **elevado**  
  A tarefa de arranque funciona com privilégios de administrador. Isto permite que as tarefas de arranque instalem programas, façam alterações de configuração do IIS, realizem alterações de registo e outras tarefas de nível de administrador, sem aumentar o nível de privilégio do próprio papel.  

> [!NOTE]
> O nível de privilégio de uma tarefa de arranque não precisa de ser o mesmo que o papel em si.
> 
> 

**taskType** - Especifica a forma como uma tarefa de arranque é executada.

* **simples**  
  As tarefas são executadas sincronizadamente, uma de cada vez, na ordem especificada no ficheiro [ServiceDefinition.csdef.] Quando uma **simples** tarefa de arranque termina com um **nível** de erro de zero, a próxima tarefa **simples** de arranque é executada. Se não houver mais tarefas **simples** de arranque para executar, então o papel em si será iniciado.   
  
  > [!NOTE]
  > Se a tarefa **simples** terminar com um **nível**de erro não zero, a instância será bloqueada. As tarefas de arranque **simples** subsequentes, e o próprio papel, não começarão.
  > 
  > 
  
    Para garantir que o ficheiro do lote termina com `EXIT /B 0` um **nível** de erro de zero, execute o comando no final do processo de ficheiro do lote.
* **fundo**  
  As tarefas são executadas assincronicamente, em paralelo com o arranque do papel.
* **primeiro plano**  
  As tarefas são executadas assincronicamente, em paralelo com o arranque do papel. A diferença fundamental entre um **primeiro plano** e uma tarefa de **fundo** é que uma tarefa de **primeiro plano** impede o papel de reciclar ou desligar até que a tarefa termine. As tarefas de **fundo** não têm esta restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
As variáveis ambientais são uma forma de passar informação para uma tarefa de arranque. Por exemplo, pode colocar o caminho para uma bolha que contenha um programa para instalar, ou números de porta que a sua função irá utilizar, ou configurações para controlar as funcionalidades da sua tarefa de arranque.

Existem dois tipos de variáveis ambientais para tarefas de arranque; variáveis de ambiente estática e variáveis ambientais baseadas em membros da classe [RoleEnvironment.] Ambos estão na secção [Ambiente] do ficheiro [ServiceDefinition.csdef,] e ambos utilizam o elemento [Variável] e o atributo de **nome.**

Variáveis de ambiente estático usam o atributo de **valor** do elemento [Variável.] O exemplo acima cria a variável ambiental **MyVersionNumber** que tem um valor estático de "**1.0.0.0**". Outro exemplo seria criar uma variável de ambiente **StagingOrProduction** que pode definir manualmente para valores de "**encenação**" ou "**produção**" para realizar diferentes ações de arranque com base no valor da variável ambiente **StagingOrProduction.**

Variáveis ambientais baseadas em membros da classe RoleEnvironment não usam **o** valor atribuído do elemento [Variável.] Em vez disso, o elemento infantil [RoleInstanceValue,] com o valor de atributo **XPath** adequado, é usado para criar uma variável ambiental baseada num membro específico da classe [RoleEnvironment.] Os valores para o atributo **XPath** para aceder a vários valores [RoleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável ambiental que seja "**verdadeira**" quando a instância está em execução no emulador computacional, e "**falso**" quando correr na nuvem, use os seguintes elementos [Variáveis] e [RoleInstanceValue:]

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
Aprenda a executar [algumas tarefas comuns](cloud-services-startup-tasks-common.md) de arranque com o seu Serviço cloud.

[Embalem](cloud-services-model-and-package.md) o seu Serviço de Cloud.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Arranque]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



