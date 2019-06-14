---
title: Utilize as dependências de tarefas para executar tarefas com base na conclusão de outras tarefas - Azure Batch | Documentos da Microsoft
description: Criar tarefas que dependem da conclusão de outras tarefas para o processamento de estilo de MapReduce e grandes quantidades de dados semelhantes cargas de trabalho no Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca6918b809a9b4ede3fffb151c7fa5183ae03b47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550402"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Criar as dependências de tarefas para executar tarefas que dependem de outras tarefas

Pode definir as dependências de tarefas para executar uma tarefa ou um conjunto de tarefas apenas depois de uma tarefa principal foi concluída. Alguns cenários em que as dependências de tarefas são úteis incluem:

* Cargas de trabalho do MapReduce estilo na cloud.
* Tarefas cujas tarefas de processamento de dados podem ser expresso como Acíclico (DAG).
* Processos de pré-processamento e pós-processamento, onde cada tarefa tem de concluir antes de poderem começar a próxima tarefa.
* Qualquer outra tarefa na qual tarefas a jusante dependem a saída de tarefas a montante.

Com as dependências de tarefas do Batch, pode criar tarefas agendadas para execução em nós de computação após a conclusão de uma ou mais tarefas de principal. Por exemplo, pode criar uma tarefa que processa cada quadro de um filme 3D com tarefas paralelas separadas. O último tarefas – a "tarefa de intercalação" – mesclagens os quadros renderizados no filme concluído somente depois que todos os quadros tem sido processados com êxito.

Por predefinição, as tarefas dependentes são agendadas para execução apenas depois da tarefa pai foi concluída com êxito. Pode especificar uma ação de dependência para substituir o comportamento predefinido e executar tarefas, quando a tarefa principal falhar. Consulte a [ações de dependência](#dependency-actions) secção para obter detalhes.  

Pode criar tarefas que dependem de outras tarefas numa relação um para um ou um-para-muitos. Também pode criar uma dependência de intervalo em que uma tarefa depende da conclusão de um grupo de tarefas dentro de um intervalo especificado de IDs de tarefa. Pode combinar estes três cenários básicos para criar relações muitos-para-muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com o Batch .NET
Neste artigo, discutimos como configurar as dependências de tarefas utilizando o [.NET do Batch] [ net_msdn] biblioteca. Primeiro mostramos como ao [ativar dependências de tarefas](#enable-task-dependencies) em seus trabalhos e, em seguida, demonstrarei como [configurar uma tarefa com dependências](#create-dependent-tasks). Também descreveremos como especificar uma ação de dependência para executar tarefas dependentes, se o elemento principal falhar. Por fim, discutiremos o [cenários de dependência](#dependency-scenarios) que o Batch suporta.

## <a name="enable-task-dependencies"></a>Ativar dependências de tarefas
Para utilizar as dependências de tarefas na sua aplicação do Batch, primeiro tem de configurar a tarefa para utilizar as dependências de tarefas. No .NET do Batch, ativá-la no seu [CloudJob] [ net_cloudjob] através da definição de seu [UsesTaskDependencies] [ net_usestaskdependencies] propriedade `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No trecho de código anterior, "batchClient" é uma instância do [BatchClient] [ net_batchclient] classe.

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que depende da conclusão das tarefas de principal de um ou mais, pode especificar que a tarefa "depende" outras tarefas. No .NET do Batch, configurar o [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] propriedade com uma instância da [TaskDependencies] [ net_taskdependencies] classe:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código cria uma tarefa dependente com o ID de tarefa "Flores". A tarefa de "Flores" depende de tarefas "Chuva" e "DOM". Tarefa "flores" irão ser agendadas para ser executada num nó de computação apenas depois de tarefas "Chuva" e "DOM" foram concluídas com êxito.

> [!NOTE]
> Por predefinição, é considerada uma tarefa seja concluída com êxito quando estiver a ser o **concluída** estado e a respetiva **código de saída** é `0`. No .NET do Batch, isso significa que uma [CloudTask][net_cloudtask].[ Estado] [ net_taskstate] o valor da propriedade de `Completed` e o CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] é o valor da propriedade `0`. Para saber como alterá-lo, consulte a [ações de dependência](#dependency-actions) secção.
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Existem três cenários de dependência na tarefa básica que pode utilizar no Azure Batch: um para um, um-para-muitos e o ID de tarefa de intervalo dependência. Estes podem ser combinados para fornecer um quarto cenário, muitos-para-muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um para um](#one-to-one) |*Tarefab* depende *Tarefaa* <p/> *Tarefab* não será possível agendar a execução até *Tarefaa* foi concluída com êxito |![Diagrama: dependência de tarefas de um para um][1] |
|  [Um-para-muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> *Tarefac* não será possível agendar a execução até que ambas *Tarefaa* e *Tarefab* foram concluídas com êxito |![Diagrama: dependências de tarefas de um-para-muitos][2] |
|  [Intervalo de ID de tarefa](#task-id-range) |*Tarefad* depende de uma variedade de tarefas <p/> *Tarefad* não será agendada para execução até as tarefas com IDs *1* através da *10* foram concluídas com êxito |![Diagrama: Dependência de intervalo de id de tarefa][3] |

> [!TIP]
> Pode criar **muitos-para-muitos** relações, por exemplo, onde tarefas C, D, E e F cada dependem de tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento em paralelo, suas tarefas a jusante dependem, em que, a saída de várias tarefas a montante.
> 
> Nos exemplos nesta secção, é executada uma tarefa dependente apenas depois das tarefas de principal são concluídas com êxito. Este comportamento é o comportamento predefinido para uma tarefa dependente. Pode executar uma tarefa dependente após a falha de uma tarefa principal ao especificar uma ação de dependência para substituir o comportamento predefinido. Consulte a [ações de dependência](#dependency-actions) secção para obter detalhes.

### <a name="one-to-one"></a>Um para um
Numa relação um para um, uma tarefa depende a conclusão com êxito da tarefa de um elemento principal. Para criar a dependência, forneça um ID de tarefa única para o [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] método estático quando preenche a [DependsOn] [ net_dependson] propriedade do [CloudTask] [ net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos
Numa relação um-para-muitos, uma tarefa depende da conclusão de várias tarefas de principal. Para criar a dependência, fornecem uma coleção de IDs de tarefas para o [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] método estático quando preenche a [DependsOn] [ net_dependson] propriedade do [CloudTask] [ net_cloudtask].

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

### <a name="task-id-range"></a>Intervalo de ID de tarefa
Numa dependência numa variedade de tarefas de principal, uma tarefa depende da conclusão de tarefas cujos IDs se encontram dentro de um intervalo.
Para criar a dependência, forneça a primeira e última IDs de tarefas no intervalo para o [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] método estático quando preenche a [DependsOn] [ net_dependson] propriedade do [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> Quando utilizar intervalos de ID de tarefa para as suas dependências, apenas as tarefas com IDs que representa valores de número inteiro serão selecionadas pelo intervalo. Portanto, o intervalo `1..10` irá selecionar tarefas `3` e `7`, mas não `5flamingoes`. 
> 
> Zeros à esquerda não são significativos ao avaliar as dependências de intervalo, para que as tarefas com identificadores de cadeias de caracteres `4`, `04` e `004` ficará *dentro* o intervalo e eles serão todos tratados como tarefa `4`, para que o primeiro a conclusão da outra irá satisfazer a dependência.
> 
> Todas as tarefas no intervalo tem de satisfazer a dependência, concluindo com êxito ou, concluindo com uma falha que está mapeada para uma ação de dependência definida como **Satisfy**. Consulte a [ações de dependência](#dependency-actions) secção para obter detalhes.
>
>

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

Por predefinição, uma tarefa dependente ou o conjunto de tarefas é executada apenas depois de uma tarefa principal foi concluída com êxito. Em alguns cenários, pode querer executar tarefas dependentes, mesmo que a tarefa principal falhar. É possível substituir o comportamento predefinido ao especificar uma ação de dependência. Uma ação de dependência Especifica se uma tarefa dependente é elegível para executar, com base no êxito ou falha da tarefa principal. 

Por exemplo, suponha que uma tarefa dependente está aguardando os dados de conclusão da tarefa a montante. Se a tarefa a montante falhar, a tarefa dependente ainda poderá executar com dados mais antigos. Neste caso, pode especificar uma ação de dependência, que a tarefa dependente é elegível para executar apesar da falha da tarefa principal.

Uma ação de dependência se baseia numa condição de saída para a tarefa principal. Pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída; para .NET, consulte a [ExitConditions] [ net_exitconditions] classe para obter mais detalhes:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de carregamento. Se a tarefa é encerrado com um código de saída que foi especificado através de **exitCodes** ou **exitCodeRanges**, e, em seguida, o erro, a ação especificada, o código de saída de carregamento de um ficheiro de encontros tem precedência.
- Quando a tarefa é encerrado com um código de saída definido pelos **ExitCodes** propriedade.
- Quando a tarefa é encerrado com um código de saída que se encontrem dentro de um intervalo especificado o **ExitCodeRanges** propriedade.
- Caso padrão, se a tarefa é encerrado com um código de saída não definido pelos **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa é encerrado com um erro de pré-processamento e a **PreProcessingError** propriedade não for definida, ou se a tarefa falhar com um ficheiro de erro de carregamento e o **FileUploadError** propriedade não está definida. 

Para especificar uma ação de dependência no .NET, defina o [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] propriedade para a condição de saída. O **DependencyAction** propriedade assume um de dois valores:

- Definindo a **DependencyAction** propriedade **Satisfy** indica que as tarefas dependentes são elegíveis para ser executada se a tarefa principal é encerrado com um erro especificado.
- Definindo a **DependencyAction** propriedade **bloco** indica que as tarefas dependentes não são elegíveis para executar.

A configuração padrão para o **DependencyAction** propriedade é **Satisfy** para o código de saída 0, e **bloco** para todas as outras condições de saída.

O seguinte código conjuntos de Trecho de código a **DependencyAction** propriedade para uma tarefa principal. Se a tarefa principal é encerrado com um erro de pré-processamento, ou com os códigos de erro especificado, a tarefa dependente é bloqueada. Se a tarefa principal é encerrado com qualquer outro erro diferente de zero, a tarefa dependente é elegível para executar.

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
O [TaskDependencies] [ github_taskdependencies] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra:

- Como ativar dependências de tarefas numa tarefa
- Como criar tarefas que dependem de outras tarefas
- Como executar essas tarefas num conjunto de nós de computação.

## <a name="next-steps"></a>Passos Seguintes
### <a name="application-deployment"></a>Implementação de aplicação
O [pacotes de aplicações](batch-application-packages.md) funcionalidade do Batch fornece uma forma fácil para ambos, implementar e a versão das aplicações que as tarefas são executadas em nós de computação.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste
Ver [de instalar aplicações e dados em lote de teste de nós de computação] [ forum_post] no fórum do Azure Batch para uma descrição geral dos métodos para preparar os nós para executar tarefas. Escrito por um dos membros da equipa do Azure Batch, esta mensagem é um bom manual sobre as diferentes formas para copiar aplicativos, dados de entrada da tarefa e outros ficheiros para os nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um para um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de id de tarefa"
