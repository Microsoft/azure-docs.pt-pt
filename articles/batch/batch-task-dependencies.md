---
title: Criar dependências de tarefas para executar tarefas
description: Crie tarefas que dependam da conclusão de outras tarefas para o processamento do estilo MapReduce e cargas de trabalho de big data semelhantes em Azure Batch.
ms.topic: how-to
ms.date: 05/22/2017
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 05b1bb289c215208be448d8ca7de144c82b313b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936984"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Criar dependências de tarefas para executar tarefas que dependem de outras tarefas

Só pode definir dependências de tarefas para executar uma tarefa ou conjunto de tarefas depois de concluída uma tarefa dos pais. Alguns cenários em que as dependências de tarefas são úteis incluem:

- Cargas de trabalho ao estilo MapReduce na nuvem.
- Empregos cujas tarefas de tratamento de dados podem ser expressas como um gráfico acíclico direcionado (DAG).
- Processos de pré-renderização e pós-renderização, onde cada tarefa deve ser concluída antes do início da próxima tarefa.
- Qualquer outro trabalho em que as tarefas a jusante dependam da produção de tarefas a montante.

Com as dependências de tarefas do Batch, pode criar tarefas que estão programadas para a execução em nós de computação após a conclusão de uma ou mais tarefas dos pais. Por exemplo, pode criar um trabalho que torne cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final- a "tarefa de fusão"-- funde os quadros renderizados no filme completo apenas depois de todos os quadros terem sido renderizados com sucesso.

Por predefinição, as tarefas dependentes só são programadas para a execução depois de a tarefa dos pais ter sido concluída com sucesso. Pode especificar uma ação de dependência para anular o comportamento predefinido e executar tarefas quando a tarefa do progenitor falhar. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.  

Pode criar tarefas que dependam de outras tarefas numa relação de um para um ou de um para muitos. Também pode criar uma dependência de alcance em que uma tarefa depende da conclusão de um grupo de tarefas dentro de um intervalo especificado de IDs de tarefa. Você pode combinar estes três cenários básicos para criar muitas para muitas relações.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com Batch .NET

Neste artigo, discutimos como configurar as dependências de tarefas utilizando a biblioteca [Batch .NET.][net_msdn] Primeiro mostramos-lhe como permitir a [dependência de tarefas](#enable-task-dependencies) dos seus trabalhos e, em seguida, demonstramos como [configurar uma tarefa com dependências.](#create-dependent-tasks) Também descrevemos como especificar uma ação de dependência para executar tarefas dependentes se o progenitor falhar. Finalmente, discutimos os [cenários de dependência](#dependency-scenarios) que o Batch apoia.

## <a name="enable-task-dependencies"></a>Ativar dependências de tarefas

Para utilizar as dependências de tarefas na sua aplicação Batch, tem primeiro de configurar o trabalho para utilizar dependências de tarefas. Em Batch .NET, ative-o no seu [CloudJob][net_cloudjob] definindo a sua propriedade [UsesTaskDependencies][net_usestaskdependencies] `true` para:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No corte de código anterior, "batchClient" é um exemplo da classe [BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes

Para criar uma tarefa que depende da conclusão de uma ou mais tarefas dos pais, pode especificar que a tarefa "depende" das outras tarefas. Em Batch .NET, configure o [CloudTask][net_cloudtask]. [Propriedade DependsOn][net_dependson] com um exemplo da classe [TaskDependencies:][net_taskdependencies]

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este código de corte cria uma tarefa dependente com a identificação da tarefa "Flores". A tarefa "Flores" depende das tarefas "Chuva" e "Sol". A tarefa "Flores" será programada para funcionar num nó de computação apenas depois de as tarefas "Rain" e "Sun" terem concluído com sucesso.

> [!NOTE]
> Por predefinição, considera-se que uma tarefa está concluída com sucesso quando se encontra no estado **preenchido** e o seu **código de saída** é `0` . Em Batch .NET, isto significa um [CloudTask][net_cloudtask]. [Valor][net_taskstate] da propriedade do Estado `Completed` e da Informação de [TaskExecution da][net_taskexecutioninformation]CloudTask .[ O][net_exitcode] valor da propriedade ExitCode é `0` . Para como mudar isto, consulte a secção [de ações de Dependência.](#dependency-actions)

## <a name="dependency-scenarios"></a>Cenários de dependência

Existem três cenários básicos de dependência de tarefas que podes usar no Azure Batch: um para um, um a muitos, e a dependência da gama de ID de tarefa. Estes podem ser combinados para proporcionar um quarto cenário, muitos para muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo | Ilustração |
|:---:| --- | --- |
|  [Um para um](#one-to-one) |*taskB* depende da *taskA* <p/> *taskB* não será agendado para execução até *que a tarefaA* tenha concluído com sucesso |![Diagrama: dependência de tarefas um-para-um][1] |
|  [Um a muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> *taskC* não será agendado para execução até que tanto *a tarefaA* como *a tarefaB* tenham concluído com sucesso |![Diagrama: dependência de tarefas de um a muitos][2] |
|  [Gama de ID de tarefa](#task-id-range) |*taskD* depende de uma gama de tarefas <p/> *taskD* não será agendado para execução até que as tarefas com iDs *1* a *10* tenham concluído com sucesso |![Diagrama: Dependência do intervalo do id de tarefa][3] |

> [!TIP]
> Você pode criar **muitas para muitas** relações, tais como onde as tarefas C, D, E e F cada um depende das tarefas A e B. Isto é útil, por exemplo, em cenários de pré-processamento paralelos onde as suas tarefas a jusante dependem da saída de múltiplas tarefas a montante.
> 
> Nos exemplos desta secção, uma tarefa dependente só funciona após a conclusão das tarefas dos pais com sucesso. Este comportamento é o comportamento padrão para uma tarefa dependente. Pode executar uma tarefa dependente após a falha de uma tarefa dos pais, especificando uma ação de dependência para anular o comportamento padrão. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.

### <a name="one-to-one"></a>Um para um

Numa relação um-para-um, uma tarefa depende da conclusão bem sucedida de uma tarefa dos pais. Para criar a dependência, forneça um único ID de tarefa às [TaskDependencies][net_taskdependencies]. Método estático [OnId][net_onid] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um a muitos

Numa relação de um para muitos, uma tarefa depende da conclusão de múltiplas tarefas dos pais. Para criar a dependência, forneça uma coleção de IDs de tarefa às [TaskDependencies][net_taskdependencies]. Método estático [OnIds][net_onids] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Gama de ID de tarefa

Numa dependência de uma série de tarefas dos pais, uma tarefa depende da conclusão de tarefas cujas IDs se situam dentro de um intervalo.
Para criar a dependência, forneça os iDs de primeira e última tarefa no intervalo às [TaskDependencies][net_taskdependencies]. Método estático [OnIdRange][net_onidrange] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando utilizar os intervalos de ID de tarefa para as suas dependências, apenas as tarefas com IDs que representam valores inteiros serão selecionadas pela gama. Assim, o intervalo `1..10` irá selecionar tarefas `3` e , mas não `7` `5flamingoes` . 
>
> Os principais zeros não são significativos na avaliação das dependências de alcance, pelo que as tarefas com identificadores de cordas `4` , `04` e todas `004` estarão *dentro* do alcance e todas serão tratadas como tarefas , pelo que a primeira a completar irá satisfazer `4` a dependência.
>
> Todas as tarefas da gama devem satisfazer a dependência, quer completando com sucesso, quer completando com uma falha que está mapeada para uma ação de dependência definida para **satisfazer**. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Ações de dependência

Por predefinição, uma tarefa dependente ou conjunto de tarefas só é executado depois de uma tarefa dos pais ter concluído com sucesso. Em alguns cenários, pode querer executar tarefas dependentes mesmo que a tarefa dos pais falhe. Pode anular o comportamento predefinido especificando uma ação de dependência. Uma ação de dependência especifica se uma tarefa dependente é elegível para executar, com base no sucesso ou insucesso da tarefa principal. 

Por exemplo, suponha que uma tarefa dependente aguarda dados da conclusão da tarefa a montante. Se a tarefa a montante falhar, a tarefa dependente poderá ainda ser capaz de executar utilizando dados mais antigos. Neste caso, uma ação de dependência pode especificar que a tarefa dependente é elegível para ser executada apesar do insucesso da tarefa principal.

Uma ação de dependência baseia-se numa condição de saída para a tarefa dos pais. Pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída; para .NET, consulte a classe [ExitConditions][net_exitconditions] para mais detalhes:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de upload de ficheiros. Se a tarefa sair com um código de saída especificado através de **saídas Códigos** ou **saídaCodeRanges**, e depois encontrar um erro de upload de ficheiros, a ação especificada pelo código de saída tem precedência.
- Quando a tarefa sai com um código de saída definido pela propriedade **ExitCodes.**
- Quando a tarefa sai com um código de saída que se enquadra num intervalo especificado pela propriedade **ExitCodeRanges.**
- O caso predefinido, se a tarefa sair com um código de saída não definido por **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa sair com um erro de pré-processamento e a propriedade **Pré-ProcessamentoError** não estiver definida, ou se a tarefa falhar com um erro de upload de ficheiros e a propriedade **FileUploadError** não estiver definida. 

Para especificar uma ação de dependência em .NET, desa estale as [Opções de Saída][net_exitoptions]. [Propriedade DependencyAction][net_dependencyaction] para a condição de saída. A **propriedade DependencyAction** tem um de dois valores:

- A **definição da propriedade DependencyAction** para **Satisfy** indica que as tarefas dependentes são elegíveis para executar se a tarefa do progenitor sair com um erro especificado.
- Definir a propriedade **DependencyAction** para **Bloco** indica que as tarefas dependentes não são elegíveis para executar.

A definição predefinição para a propriedade **DependencyAction** é **Satisfy** for exit code 0 e **Block** para todas as outras condições de saída.

O seguinte código snippet define a propriedade **DependencyAction** para uma tarefa principal. Se a tarefa do progenitor sair com um erro de pré-processamento, ou com os códigos de erro especificados, a tarefa dependente é bloqueada. Se a tarefa do progenitor sair com qualquer outro erro não zero, a tarefa dependente é elegível para executar.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Exemplo de código

O projeto de amostra [TaskDependencies][github_taskdependencies] é uma das amostras de código do [Azure Batch][github_samples] no GitHub. Esta solução visual Studio demonstra:

- Como permitir a dependência de tarefas de um trabalho
- Como criar tarefas que dependam de outras tarefas
- Como executar essas tarefas num conjunto de nós computacional.

## <a name="next-steps"></a>Passos seguintes

- A funcionalidade de pacotes de [aplicações](batch-application-packages.md) do Batch fornece uma maneira fácil de implementar e ver versão as aplicações que as suas tarefas executam nos nós computacional.
- Consulte [a instalação de aplicações e os dados de paragem dos nós de computação batch][forum_post] no fórum Azure Batch para obter uma visão geral dos métodos para preparar os seus nós para executar tarefas. Escrito por um dos membros da equipa do Azure Batch, este post é um bom primer sobre as diferentes formas de copiar aplicações, dados de entrada de tarefas e outros ficheiros para os seus nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_dependson]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_exitcode]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_exitconditions]: /dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_onid]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onids]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onidrange]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_taskexecutioninformation]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_taskstate]: /dotnet/api/microsoft.azure.batch.common.taskstate
[net_usestaskdependencies]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_taskdependencies]: /dotnet/api/microsoft.azure.batch.taskdependencies

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência um-para-um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um a muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência do intervalo de id de tarefa"
