---
title: Executar Tarefas de Startup nos Serviços Azure Cloud Microsoft Docs
description: As tarefas de arranque ajudam a preparar o seu ambiente de serviço na nuvem para a sua aplicação. Isto ensina-lhe como as tarefas de arranque funcionam e como fazê-las
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: f2417389de98f9998c189e7cbbbcdae77fbb8840
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020709"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de arranque para um serviço de cloud
Pode utilizar tarefas de arranque para executar operações antes de começar uma função. As operações que poderá querer realizar incluem a instalação de um componente, o registo de componentes com COM, a definição de chaves de registo ou o início de um longo processo de funcionamento.

> [!NOTE]
> As tarefas de arranque não são aplicáveis às Máquinas Virtuais, apenas às funções web e trabalhadora do Cloud Service.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de arranque
As tarefas de arranque são ações que são tomadas antes do início das suas funções e são definidas no ficheiro [ServiceDefinition.csdef] utilizando o elemento [Tarefa] dentro do elemento [Iniciar.] As tarefas de arranque frequentes são ficheiros de lote, mas também podem ser aplicações de consola ou ficheiros de lote que iniciam scripts PowerShell.

As variáveis ambientais passam a informação para uma tarefa de arranque, e o armazenamento local pode ser usado para passar informações de uma tarefa de arranque. Por exemplo, uma variável ambiental pode especificar o caminho para um programa que pretende instalar, e os ficheiros podem ser escritos para armazenamento local que podem ser lidos mais tarde pelas suas funções.

A sua tarefa de arranque pode registar informações e erros no diretório especificado pela variável ambiente **TEMP.** Durante a tarefa de arranque, a variável **ambiente TEMP** resolve o *C: \\ Recursos \\ \\ temporários [guid].. rolename] \\ RoleTemp* diretório quando corre na nuvem.

As tarefas de arranque também podem ser executadas várias vezes entre reinícios. Por exemplo, a tarefa de arranque será executada sempre que a função reciclar e a reciclagem da função pode não incluir sempre um reinício. As tarefas de arranque devem ser escritas de uma forma que lhes permita executar várias vezes sem problemas.

As tarefas de arranque devem terminar com um **erro de nível** (ou código de saída) de zero para que o processo de arranque esteja concluído. Se uma tarefa de arranque terminar com um **nível** de erro não zero, a função não será iniciada.

## <a name="role-startup-order"></a>Ordem de arranque de função
As seguintes listas listam o procedimento de arranque de funções em Azure:

1. O caso é marcado como **Iniciar** e não recebe tráfego.
2. Todas as tarefas de arranque são executadas de acordo com o atributo **taskType.**
   
   * As tarefas **simples** são executadas sincronizadamente, uma de cada vez.
   * As tarefas **de fundo** e de **primeiro plano** são iniciadas de forma assíncrona, paralelamente à tarefa de arranque.  
     
     > [!WARNING]
     > O IIS pode não estar totalmente configurado durante a fase de tarefa de arranque no processo de arranque, pelo que os dados específicos da função podem não estar disponíveis. As tarefas de arranque que requerem dados específicos de funções devem utilizar [o Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. O processo de anfitrião de funções é iniciado e o site é criado no IIS.
4. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) é chamado.
5. O caso está marcado como **Ready** e o tráfego é encaminhado para o caso.
6. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de arranque
As tarefas de arranque são definidas no ficheiro [ServiceDefinition.csdef,] no elemento **Tarefa.** O atributo **commandLine** especifica o nome e os parâmetros do ficheiro de lote de arranque ou do comando da consola, o atributo **de execuçãoContexto** especifica o nível de privilégio da tarefa de arranque, e o atributo **taskType** especifica como a tarefa será executada.

Neste exemplo, uma variável ambiental, **MyVersionNumber,** é criada para a tarefa de arranque e definida ao valor "**1.0.0.0".**

**ServiceDefinition.csdef:**

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo seguinte, o ficheiro de lote **Startup.cmd** escreve a linha "A versão atual é 1.0.0.0" para o ficheiro StartupLog.txt no diretório especificado pela variável ambiente TEMP. A `EXIT /B 0` linha garante que a tarefa de arranque termina com um **nível de erro** de zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, a propriedade **Copy to Output Directory** para o seu ficheiro de lote de arranque deve ser definida para **Copy Always** para ter certeza de que o seu ficheiro de lote de arranque está devidamente implantado no seu projeto no Azure **\\ (approot bin** para funções Web, e **aproximadamente** para funções de trabalhador).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição de atributos de tarefas
O seguinte descreve os atributos do elemento **Tarefa** no ficheiro [ServiceDefinition.csdef:]

**commandLine** - Especifica a linha de comando para a tarefa de arranque:

* O comando, com parâmetros de linha de comando opcionais, que inicia a tarefa de arranque.
* Frequentemente este é o nome de ficheiro de um ficheiro .cmd ou .bat lote.
* A tarefa é relativa à pasta AppRoot \\ Bin para a implantação. As variáveis ambientais não são expandidas na determinação do caminho e do arquivo da tarefa. Se for necessária expansão ambiental, pode criar um pequeno script .cmd que chama a sua tarefa de arranque.
* Pode ser uma aplicação de consola ou um ficheiro de lote que inicia um [script PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**execuçãoContexto** - Especifica o nível de privilégio para a tarefa de arranque. O nível de privilégio pode ser limitado ou elevado:

* **limitado**  
  A tarefa de arranque tem os mesmos privilégios que o papel. Quando o atributo **de execuçãoContexto** para o elemento [Runtime] também é **limitado,** então os privilégios do utilizador são utilizados.
* **elevado**  
  A tarefa de arranque funciona com privilégios de administrador. Isto permite que as tarefas de arranque instalem programas, façam alterações na configuração do IIS, realizem alterações de registo e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da função em si.  

> [!NOTE]
> O nível de privilégio de uma tarefa de startup não precisa de ser o mesmo que o papel em si.
> 
> 

**taskType** - Especifica a forma como uma tarefa de arranque é executada.

* **simples**  
  As tarefas são executadas sincronizadamente, uma de cada vez, na ordem especificada no ficheiro [ServiceDefinition.csdef.] Quando uma **simples** tarefa de arranque termina com um **erro** de zero, a próxima tarefa **simples** de arranque é executada. Se não houver mais tarefas **simples** de arranque para executar, então o papel em si será iniciado.   
  
  > [!NOTE]
  > Se a **tarefa simples** terminar com um **nível** de erro não zero, a instância será bloqueada. As tarefas de arranque **simples** subsequentes, e o papel em si, não serão iniciados.
  > 
  > 
  
    Para garantir que o seu ficheiro de lote termina com um **erro** de zero, execute o comando `EXIT /B 0` no final do processo de ficheiro do lote.
* **background**  
  As tarefas são executadas assíncronamente, em paralelo com o arranque do papel.
* **primeiro plano**  
  As tarefas são executadas assíncronamente, em paralelo com o arranque do papel. A principal diferença entre um **primeiro plano** e uma tarefa **de fundo** é que uma tarefa de **primeiro plano** impede que o papel seja reciclado ou desligado até que a tarefa termine. As tarefas **de fundo** não têm esta restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
As variáveis ambientais são uma forma de passar informação para uma tarefa de arranque. Por exemplo, pode colocar o caminho para uma bolha que contém um programa para instalar, ou números de porta que a sua função irá utilizar, ou configurações para controlar as funcionalidades da sua tarefa de arranque.

Existem dois tipos de variáveis ambientais para tarefas de arranque; variáveis de ambiente estática e variáveis ambientais baseadas em membros da classe [RoleEnvironment.] Ambos estão na secção [Ambiente] do ficheiro [ServiceDefinition.csdef,] e ambos usam o elemento [variável] e o atributo **de nome.**

Variáveis de ambiente estático usam o atributo de **valor** do elemento [Variável.] O exemplo acima cria a variável ambiente **MyVersionNumber** que tem um valor estático de "**1.0.0.0**". Outro exemplo seria criar uma variável ambiente **stagingOrProduction** que pode definir manualmente para valores de "**encenação**" ou "**produção**" para executar diferentes ações de arranque com base no valor da variável ambiente **StagingOrProduction.**

Variáveis ambientais baseadas em membros da classe RoleEnvironment não usam o atributo de **valor** do elemento [Variável.] Em vez disso, o elemento infantil [RoleInstanceValue,] com o valor de atributo **XPath** apropriado, é usado para criar uma variável ambiental baseada num membro específico da classe [RoleEnvironment.] Os valores para o atributo **XPath** para aceder a vários valores [roleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável ambiental que é "**verdadeira**" quando a instância está a decorrer no emulador de computação, e "**falsa**" ao correr na nuvem, use os seguintes elementos [Variáveis] e [RoleInstanceValue:]

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
Saiba como executar algumas [tarefas comuns de arranque](cloud-services-startup-tasks-common.md) com o seu Serviço Cloud.

[Embale](cloud-services-model-and-package.md) o seu Serviço cloud.  

[ServiçoDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Arranque]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Environment]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variável]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)