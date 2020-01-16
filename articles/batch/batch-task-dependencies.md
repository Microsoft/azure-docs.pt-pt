---
title: Usar dependências de tarefas para executar tarefas com base na conclusão de outras tarefas – lote do Azure | Microsoft Docs
description: Crie tarefas que dependem da conclusão de outras tarefas para processamento de estilo MapReduce e cargas de trabalho de Big Data semelhantes no lote do Azure.
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 875e0314c41a6bb277769361b6faa0345312db2b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026243"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Criar dependências de tarefas para executar tarefas que dependem de outras tarefas

Você pode definir dependências de tarefas para executar uma tarefa ou um conjunto de tarefas somente após a conclusão de uma tarefa pai. Alguns cenários em que as dependências de tarefas são úteis incluem:

* Cargas de trabalho em estilo MapReduce na nuvem.
* Trabalhos cujas tarefas de processamento de dados podem ser expressas como um DAG (grafo direcionado acíclico).
* Processos de pré-processamento e pós-processamento, onde cada tarefa deve ser concluída antes que a próxima tarefa possa começar.
* Qualquer outro trabalho no qual as tarefas downstream dependem da saída de tarefas upstream.

Com as dependências de tarefas do lote, você pode criar tarefas que são agendadas para execução em nós de computação após a conclusão de uma ou mais tarefas pai. Por exemplo, você pode criar um trabalho que renderiza cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final – a "tarefa de mesclagem" – mescla os quadros renderizados no filme completo somente depois que todos os quadros tiverem sido renderizados com êxito.

Por padrão, as tarefas dependentes são agendadas para execução somente depois que a tarefa pai é concluída com êxito. Você pode especificar uma ação de dependência para substituir o comportamento padrão e executar tarefas quando a tarefa pai falhar. Consulte a seção [ações de dependência](#dependency-actions) para obter detalhes.  

Você pode criar tarefas que dependem de outras tarefas em uma relação um-para-um ou um-para-muitos. Você também pode criar uma dependência de intervalo em que uma tarefa depende da conclusão de um grupo de tarefas dentro de um intervalo especificado de IDs de tarefa. Você pode combinar esses três cenários básicos para criar relações muitos para muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com .NET do lote
Neste artigo, discutiremos como configurar dependências de tarefas usando a biblioteca [.net do lote][net_msdn] . Primeiro mostramos como habilitar a [dependência de tarefas](#enable-task-dependencies) em seus trabalhos e, em seguida, demonstram como [Configurar uma tarefa com dependências](#create-dependent-tasks). Também descreveremos como especificar uma ação de dependência para executar tarefas dependentes se o pai falhar. Por fim, discutimos os [cenários de dependência](#dependency-scenarios) aos quais o lote dá suporte.

## <a name="enable-task-dependencies"></a>Habilitar dependências de tarefas
Para usar dependências de tarefas em seu aplicativo do lote, você deve primeiro configurar o trabalho para usar dependências de tarefas. No .NET do lote, habilite-o em seu [CloudJob][net_cloudjob] definindo sua propriedade [UsesTaskDependencies][net_usestaskdependencies] como `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No trecho de código anterior, "batchClient" é uma instância da classe [batchClient][net_batchclient] .

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que depende da conclusão de uma ou mais tarefas pai, você pode especificar que a tarefa "depende" das outras tarefas. No .NET do lote, configure o [CloudTask][net_cloudtask]. [Depende][net_dependson] da propriedade com uma instância da classe [TaskDependencies][net_taskdependencies] :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este trecho de código cria uma tarefa dependente com a ID da tarefa "flores". A tarefa "flores" depende das tarefas "Rain" e "sol". A tarefa "flores" será agendada para ser executada em um nó de computação somente após as tarefas "Rain" e "sol" terem sido concluídas com êxito.

> [!NOTE]
> Por padrão, uma tarefa é considerada concluída com êxito quando está no estado **concluído** e seu **código de saída** é `0`. No .NET do lote, isso significa um [CloudTask][net_cloudtask]. Valor da propriedade [State][net_taskstate] de `Completed` e [TaskExecutionInformation][net_taskexecutioninformation]do CloudTask. O valor da propriedade [ExitCode][net_exitcode] é `0`. Para saber como alterar isso, consulte a seção [ações de dependência](#dependency-actions) .
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Há três cenários básicos de dependência de tarefas que você pode usar no lote do Azure: um-para-um, um-para-muitos e dependência de intervalo de IDs de tarefas. Eles podem ser combinados para fornecer um quarto cenário, muitos para muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um para um](#one-to-one) |*da Tarefab* depende da *tarefaA* <p/> *da Tarefab* não será agendado para execução até que a *tarefaA* seja concluída com êxito |![Diagrama: dependência de tarefa um-para-um][1] |
|  [Um para muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> *a tarefac* não será agendado para execução até que ambas as *tarefas* e *da Tarefab* tenham sido concluídas com êxito |![Diagrama: dependência de tarefa um-para-muitos][2] |
|  [Intervalo de ID de tarefa](#task-id-range) |a *tarefa* depende de um intervalo de tarefas <p/> a *tarefa* não será agendada para execução até que as tarefas com as IDs de *1* a *10* tenham sido concluídas com êxito |![Diagrama: dependência de intervalo de IDs de tarefas][3] |

> [!TIP]
> Você pode criar relações **muitos para muitos** , como onde as tarefas C, D, e e F dependem das tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento paralelizados em que as tarefas downstream dependem da saída de várias tarefas upstream.
> 
> Nos exemplos nesta seção, uma tarefa dependente é executada somente depois que as tarefas pai são concluídas com êxito. Esse comportamento é o comportamento padrão para uma tarefa dependente. Você pode executar uma tarefa dependente após a falha de uma tarefa pai, especificando uma ação de dependência para substituir o comportamento padrão. Consulte a seção [ações de dependência](#dependency-actions) para obter detalhes.

### <a name="one-to-one"></a>Um para um
Em uma relação um-para-um, uma tarefa depende da conclusão bem-sucedida de uma tarefa pai. Para criar a dependência, forneça uma única ID de tarefa para o [TaskDependencies][net_taskdependencies]. Método estático [OnId][net_onid] quando você preenche a propriedade [dependy][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um para muitos
Em uma relação um-para-muitos, uma tarefa depende da conclusão de várias tarefas pai. Para criar a dependência, forneça uma coleção de IDs de tarefa para o [TaskDependencies][net_taskdependencies]. Método estático [OnIds][net_onids] quando você preenche a propriedade [dependy][net_dependson] de [CloudTask][net_cloudtask].

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
Em uma dependência em um intervalo de tarefas pai, uma tarefa depende da conclusão das tarefas cujas IDs estão dentro de um intervalo.
Para criar a dependência, forneça a primeira e a última ID de tarefa no intervalo para o [TaskDependencies][net_taskdependencies]. Método estático [OnIdRange][net_onidrange] quando você preenche a propriedade [dependy][net_dependson] de [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando você usa intervalos de ID de tarefa para suas dependências, somente as tarefas com IDs que representam valores inteiros serão selecionadas pelo intervalo. Portanto, o intervalo `1..10` selecionará as tarefas `3` e `7`, mas não `5flamingoes`. 
> 
> Os zeros à esquerda não são significativos durante a avaliação de dependências de intervalo, portanto, as tarefas com identificadores de cadeia de caracteres `4`, `04` e `004` estarão *dentro* do intervalo e todas serão tratadas como `4`de tarefas, portanto, a primeira a ser concluída atenderá à dependência.
> 
> Cada tarefa no intervalo deve satisfazer a dependência, seja concluída com êxito ou concluindo com uma falha que é mapeada para uma ação de dependência definida para **satisfazer**. Consulte a seção [ações de dependência](#dependency-actions) para obter detalhes.
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

Por padrão, uma tarefa dependente ou um conjunto de tarefas é executado somente depois que uma tarefa pai é concluída com êxito. Em alguns cenários, talvez você queira executar tarefas dependentes, mesmo se a tarefa pai falhar. Você pode substituir o comportamento padrão especificando uma ação de dependência. Uma ação de dependência especifica se uma tarefa dependente está qualificada para execução, com base no êxito ou na falha da tarefa pai. 

Por exemplo, suponha que uma tarefa dependente esteja aguardando dados da conclusão da tarefa de upstream. Se a tarefa de upstream falhar, a tarefa dependente ainda poderá ser capaz de executar usando dados mais antigos. Nesse caso, uma ação de dependência pode especificar que a tarefa dependente seja qualificada para ser executada, apesar da falha da tarefa pai.

Uma ação de dependência é baseada em uma condição de saída para a tarefa pai. Você pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída; para .NET, consulte a classe [ExitConditions][net_exitconditions] para obter detalhes:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de upload de arquivo. Se a tarefa for encerrada com um código de saída especificado por meio de **exitCodes** ou **exitCodeRanges**e, em seguida, encontrar um erro de carregamento de arquivo, a ação especificada pelo código de saída terá precedência.
- Quando a tarefa é encerrada com um código de saída definido pela propriedade **ExitCodes** .
- Quando a tarefa é encerrada com um código de saída que está dentro de um intervalo especificado pela propriedade **ExitCodeRanges** .
- O caso padrão, se a tarefa for encerrada com um código de saída não definido por **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa for encerrada com um erro de pré-processamento e a propriedade **PreProcessingError** não estiver definida, ou se a tarefa falhar com um erro de upload de arquivo e a propriedade **FileUploadError** não estiver definida. 

Para especificar uma ação de dependência no .NET, defina [exitoptions][net_exitoptions]. Propriedade [dependencyaction][net_dependencyaction] para a condição de saída. A propriedade **dependencyaction** usa um dos dois valores:

- Definir a propriedade **dependencyaction** como **satisfazer** indica que as tarefas dependentes serão elegíveis para execução se a tarefa pai for encerrada com um erro especificado.
- Definir a propriedade **dependencyaction** como **Block** indica que as tarefas dependentes não estão qualificadas para execução.

A configuração padrão para a propriedade **dependencyaction** é **satisfazer** para o código de saída 0 e **Bloquear** para todas as outras condições de saída.

O trecho de código a seguir define a propriedade **dependencyaction** para uma tarefa pai. Se a tarefa pai for encerrada com um erro de pré-processamento ou com os códigos de erro especificados, a tarefa dependente será bloqueada. Se a tarefa pai for encerrada com qualquer outro erro diferente de zero, a tarefa dependente será qualificada para execução.

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
O projeto de exemplo [TaskDependencies][github_taskdependencies] é um dos [exemplos de código do lote do Azure][github_samples] no github. Esta solução do Visual Studio demonstra:

- Como habilitar a dependência de tarefas em um trabalho
- Como criar tarefas que dependem de outras tarefas
- Como executar essas tarefas em um pool de nós de computação.

## <a name="next-steps"></a>Passos seguintes
### <a name="application-deployment"></a>Implementação de aplicações
O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil de implantar e executar a versão dos aplicativos que suas tarefas executam em nós de computação.

### <a name="installing-applications-and-staging-data"></a>Instalando aplicativos e preparando dados
Consulte [Instalando aplicativos e preparando dados em nós de computação do lote][forum_post] no fórum do lote do Azure para obter uma visão geral dos métodos para preparar seus nós para executar tarefas. Escrito por um dos membros da equipe do lote do Azure, esta postagem é um bom passo a passo sobre as diferentes maneiras de copiar aplicativos, dados de entrada de tarefa e outros arquivos para seus nós de computação.

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

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um-para-um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de IDs de tarefas"
