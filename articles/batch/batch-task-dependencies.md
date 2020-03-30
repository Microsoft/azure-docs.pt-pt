---
title: Criar dependências de tarefas para executar tarefas - Lote Azure
description: Crie tarefas que dependam da conclusão de outras tarefas para o processamento do estilo MapReduce e cargas de trabalho semelhantes de big data em Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022907"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Criar dependências de tarefas para executar tarefas que dependem de outras tarefas

Só pode definir dependências de tarefas para executar uma tarefa ou conjunto de tarefas apenas depois de concluída uma tarefa-mãe. Alguns cenários em que as dependências de tarefas são úteis incluem:

* Cargas de trabalho em estilo MapReduce na nuvem.
* Empregos cujas tarefas de processamento de dados podem ser expressas como um gráfico acíclico direcionado (DAG).
* Processos de pré-renderização e pós-renderização, onde cada tarefa deve ser concluída antes de começar a próxima tarefa.
* Qualquer outro trabalho em que as tarefas a jusante dependam da produção de tarefas a montante.

Com as dependências de tarefas do Lote, pode criar tarefas que estão programadas para a execução em nós de cálculo após a conclusão de uma ou mais tarefas-mãe. Por exemplo, pode criar um trabalho que torne cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final- a "tarefa de fusão"--- une os quadros renderizados no filme completo apenas depois de todos os quadros terem sido entregues com sucesso.

Por padrão, as tarefas dependentes só estão programadas para a execução após a tarefa dos pais ter concluído com sucesso. Pode especificar uma ação de dependência para anular o comportamento padrão e executar tarefas quando a tarefa dos pais falhar. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.  

Pode criar tarefas que dependem de outras tarefas numa relação um-para-um ou de um para muitas. Também pode criar uma dependência de alcance onde uma tarefa depende da conclusão de um conjunto de tarefas dentro de um intervalo especificado de IDs de tarefa. Você pode combinar estes três cenários básicos para criar muitas a muitas relações.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com Lote .NET
Neste artigo, discutimos como configurar as dependências de tarefas utilizando a biblioteca [Batch .NET.][net_msdn] Primeiro mostramos-lhe como permitir a dependência da [tarefa](#enable-task-dependencies) dos seus trabalhos e, em seguida, demonstramos como [configurar uma tarefa com dependências](#create-dependent-tasks). Também descrevemos como especificar uma ação de dependência para executar tarefas dependentes se o progenitor falhar. Finalmente, discutimos os [cenários](#dependency-scenarios) de dependência que batch apoia.

## <a name="enable-task-dependencies"></a>Ativar dependências de tarefas
Para utilizar as dependências de tarefas na sua aplicação Batch, tem primeiro de configurar o trabalho para utilizar dependências de tarefas. Em Batch .NET, ative-o no seu [CloudJob,][net_cloudjob] `true`definindo a sua propriedade [UseTaskDependencies][net_usestaskdependencies] para:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No código anterior, "batchClient" é uma instância da classe [BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que dependa da conclusão de uma ou mais tarefas-mãe, pode especificar que a tarefa "depende" das outras tarefas. No Lote .NET, configure a [CloudTask][net_cloudtask]. [Depende da][net_dependson] propriedade com um exemplo da classe [TaskDependencies:][net_taskdependencies]

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este código de corte cria uma tarefa dependente com id de tarefa "Flores". A tarefa "Flores" depende das tarefas "Chuva" e "Sol". A tarefa "Flores" só será executada num nó computacional depois de as tarefas "Rain" e "Sun" terem concluído com sucesso.

> [!NOTE]
> Por padrão, considera-se que uma tarefa é concluída com sucesso quando se `0`encontra no estado **concluído** e o seu código de **saída** é . No Lote .NET, isto significa uma [CloudTask][net_cloudtask]. [Valor][net_taskstate] da `Completed` propriedade do Estado e [a Informação][net_taskexecutioninformation]de Execução de Tarefas da CloudTask. O valor da `0`propriedade do [ExitCode][net_exitcode] é . Para como mudar isto, consulte a secção de [ações de Dependência.](#dependency-actions)
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Existem três cenários básicos de dependência de tarefas que pode utilizar no Lote Azure: um para um, um a muitos, e a dependência do alcance de id de tarefa. Estes podem ser combinados para fornecer um quarto cenário, muitos a muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um para um](#one-to-one) |*taskB* depende da *tarefaA* <p/> *taskB* não será programado para execução até que a *taskA* tenha concluído com sucesso |![Diagrama: dependência de tarefa si][1] |
|  [Um a muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> *taskC* não será programado para execução até que tanto a *taskA* como a *taskB* tenham concluído com sucesso |![Diagrama: dependência de tarefas de um a muitos][2] |
|  [Gama de ID de tarefa](#task-id-range) |*taskD* depende de um leque de tarefas <p/> *taskD* não será programado para execução até que as tarefas com IDs *1* a *10* tenham concluído com sucesso |![Diagrama: Dependência de alcance de id de tarefa][3] |

> [!TIP]
> Você pode criar **muitas a muitas** relações, tais como onde as tarefas C, D, E e F dependem cada uma das tarefas A e B. Isto é útil, por exemplo, em cenários de pré-processamento paralelizados onde as suas tarefas a jusante dependem da saída de múltiplas tarefas a montante.
> 
> Nos exemplos desta secção, uma tarefa dependente só funciona após as tarefas dos pais concluídas com sucesso. Este comportamento é o comportamento padrão para uma tarefa dependente. Pode executar uma tarefa dependente após a falha de uma tarefa dos pais, especificando uma ação de dependência para anular o comportamento padrão. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.

### <a name="one-to-one"></a>Um para um
Numa relação um-para-um, uma tarefa depende do sucesso da conclusão de uma tarefa-mãe. Para criar a dependência, forneça uma única identificação de tarefa às [TaskDependencies][net_taskdependencies]. Método estático [OnId][net_onid] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

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
Numa relação de um a muitos, uma tarefa depende da conclusão de múltiplas tarefas dos pais. Para criar a dependência, forneça uma coleção de IDs de tarefa sintetizadoras para as [TaskDependencies][net_taskdependencies]. Método estático [OnIds][net_onids] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

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
Numa dependência de uma série de tarefas-mãe, uma tarefa depende da conclusão de tarefas cujos IDs se encontram dentro de um intervalo.
Para criar a dependência, forneça as primeiras e últimas tarefas iDs na gama para as [TaskDependencies][net_taskdependencies]. Método estático [OnIdRange][net_onidrange] quando povoa a propriedade [DependsOn][net_dependson] da [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando utilizar intervalos de ID de tarefa para as suas dependências, apenas as tarefas com IDs que representam valores inteiros serão selecionadas pela gama. Assim, o `1..10` intervalo irá `3` `7`selecionar tarefas e, mas não. `5flamingoes` 
> 
> Os zeros principais não são significativos na avaliação das `4`dependências de alcance, pelo que as tarefas com identificadores de cordas , `04` e `004` todas estarão *dentro* do alcance e todas serão tratadas como tarefa `4`, pelo que a primeira a completar irá satisfazer a dependência.
> 
> Todas as tarefas na gama devem satisfazer a dependência, quer completando com sucesso, quer completando com uma falha que está mapeada para uma ação de dependência definida para **satisfazer**. Consulte a secção [de ações de Dependência](#dependency-actions) para obter mais detalhes.
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

Por padrão, uma tarefa dependente ou um conjunto de tarefas só funciona depois de uma tarefa-mãe ter concluído com sucesso. Em alguns cenários, pode querer executar tarefas dependentes mesmo que a tarefa dos pais falhe. Pode anular o comportamento padrão especificando uma ação de dependência. Uma ação de dependência especifica se uma tarefa dependente é elegível para executar, com base no sucesso ou insucesso da tarefa-mãe. 

Por exemplo, suponha que uma tarefa dependente aguarda dados da conclusão da tarefa a montante. Se a tarefa a montante falhar, a tarefa dependente poderá ainda ser executada utilizando dados mais antigos. Neste caso, uma ação de dependência pode especificar que a tarefa dependente é elegível para executar, apesar da falha da tarefa-mãe.

Uma ação de dependência baseia-se numa condição de saída para a tarefa dos pais. Pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída; para .NET, consulte a classe [ExitConditions][net_exitconditions] para obter mais detalhes:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de upload de ficheiros. Se a tarefa sair com um código de saída especificado através de **Códigos** de saída ou **de saídaCodeRanges**, e depois encontrar um erro de upload de ficheiros, a ação especificada pelo código de saída tem precedência.
- Quando a tarefa sai com um código de saída definido pela propriedade **ExitCodes.**
- Quando a tarefa sai com um código de saída que se insere dentro de um intervalo especificado pela propriedade **ExitCodeRanges.**
- O caso predefinido, se a tarefa sair com um código de saída não definido por **ExitCodes** ou **ExitCodeRanges,** ou se a tarefa sair com um erro de pré-processamento e a propriedade **PreProcessingError** não for definida, ou se a tarefa falhar com um erro de upload de ficheiros e a propriedade **FileUploadError** não estiver definida. 

Para especificar uma ação de dependência em .NET, detete as [Opções de Saída][net_exitoptions]. [Propriedade DependencyAction][net_dependencyaction] para o estado de saída. A propriedade **DependencyAction** tem um de dois valores:

- Definir a propriedade **DependencyAction** para **Satisfazer** indica que as tarefas dependentes são elegíveis para executar se a tarefa-mãe sair com um erro especificado.
- Definir a propriedade **DependencyAction** para **Bloquear** indica que as tarefas dependentes não são elegíveis para executar.

A definição padrão para a propriedade **DependencyAction** é **Satisfy** para o código de saída 0, e **Bloquear** para todas as outras condições de saída.

O seguinte código de corte define a propriedade **DependencyAction** para uma tarefa-mãe. Se a tarefa dos pais sair com um erro de pré-processamento, ou com os códigos de erro especificados, a tarefa dependente está bloqueada. Se a tarefa-mãe sair com qualquer outro erro não zero, a tarefa dependente é elegível para ser executada.

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
O projeto de amostra [TaskDependencies][github_taskdependencies] é uma das amostras de [código do Lote Azure][github_samples] no GitHub. Esta solução De Estúdio Visual demonstra:

- Como permitir a dependência da tarefa num trabalho
- Como criar tarefas que dependem de outras tarefas
- Como executar essas tarefas num conjunto de nódosos de computação.

## <a name="next-steps"></a>Passos seguintes
### <a name="application-deployment"></a>Implementação de aplicações
A funcionalidade de pacotes de [aplicação](batch-application-packages.md) do Batch fornece uma maneira fácil de implementar e verver as aplicações que as suas tarefas executam em nós de cálculo.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicações e dados de encenação
Consulte a instalação de [aplicações e dados de preparação em nódos][forum_post] de computação de Lote no fórum Do Lote Azure para obter uma visão geral dos métodos para preparar os seus nós para executar tarefas. Escrito por um dos membros da equipa do Lote Azure, este post é um bom primor sobre as diferentes formas de copiar aplicações, dados de entrada de tarefas e outros ficheiros para os seus nós de computação.

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
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um a muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência do alcance do id de tarefa"
