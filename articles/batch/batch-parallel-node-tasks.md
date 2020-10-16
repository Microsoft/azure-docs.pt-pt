---
title: Executar tarefas paralelamente para otimizar recursos computacional
description: Aumentar a eficiência e reduzir os custos utilizando menos nós de computação e executando tarefas simultâneas em cada nó num pool do Azure Batch
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851004"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Executar tarefas simultaneamente para maximizar o uso de nó de computação batch

Ao executar mais do que uma tarefa simultaneamente em cada nó de cálculo na sua piscina Azure Batch, pode maximizar o uso de recursos num número menor de nós na piscina. Para algumas cargas de trabalho, isto pode resultar em tempos de trabalho mais curtos e custos mais baixos.

Embora alguns cenários beneficiem de dedicar todos os recursos de um nó a uma única tarefa, várias situações beneficiam de permitir que várias tarefas partilhem esses recursos:

* **Minimizar a transferência de dados** quando as tarefas são capazes de partilhar dados. Neste cenário, pode reduzir drasticamente os encargos de transferência de dados copiando dados partilhados para um número menor de nós e executando tarefas paralelas a cada nó. Isto aplica-se especialmente se os dados a copiar para cada nó tão devem ser transferidos entre regiões geográficas.
* **Maximizar o uso** da memória quando as tarefas requerem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo, e em tempos variáveis durante a execução. Pode empregar menos, mas maiores, nós computacional com mais memória para lidar eficientemente com esses picos. Estes nós teriam múltiplas tarefas em paralelo em cada nó, mas cada tarefa tiraria partido da memória abundante dos nós em diferentes momentos.
* **Atenuando os limites do número do nó** quando é necessária uma comunicação inter-nó node dentro de uma piscina. Atualmente, as piscinas configuradas para a comunicação entre nós estão limitadas a 50 nós computacional. Se cada nó em tal piscina for capaz de executar tarefas em paralelo, um maior número de tarefas pode ser executado simultaneamente.
* **Replicando um cluster compute no local,** como quando se move um ambiente computacional para Azure. Se a sua solução atual no local executar múltiplas tarefas por nó de computação, pode aumentar o número máximo de tarefas de nó para espelhar mais de perto essa configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Como exemplo para ilustrar os benefícios da execução paralela da tarefa, digamos que a sua aplicação de tarefa tem CPU e requisitos de memória de tal forma que os nós [ \_ Standard D1](../cloud-services/cloud-services-sizes-specs.md) são suficientes. Mas, para terminar o trabalho no tempo necessário, são necessários 1.000 destes nós.

Em vez de utilizar \_ nós Standard D1 que tenham 1 núcleo CPU, pode utilizar nós [Standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) que têm 16 núcleos cada, e permitir a execução paralela da tarefa. Portanto, *16 vezes menos nós* poderiam ser usados -- em vez de 1.000 nós, apenas 63 seriam necessários. Além disso, se forem necessários grandes ficheiros de aplicações ou dados de referência para cada nó, a duração e eficiência do trabalho são novamente melhoradas, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Permitir a execução paralela de tarefas
Configura os nós computacional para execução paralela de tarefas ao nível da piscina. Com a biblioteca Batch .NET, deslove a propriedade [CloudPool.TaskSlotsPerNode][maxtasks_net] quando criar uma piscina. Se estiver a utilizar a API de Lote REST, desloque o elemento [TaskSlotsPerNode][rest_addpool] no corpo de pedido durante a criação da piscina.

O Azure Batch permite-lhe definir ranhuras de tarefa por nó até (4x) o número de núcleos de nó. Por exemplo, se a piscina estiver configurada com nós de tamanho "Grande" (quatro núcleos), `taskSlotsPerNode` pode ser configurado para 16. No entanto, independentemente de quantos núcleos o nó tem, não pode ter mais de 256 slots de tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos dos nós, consulte [tamanhos para serviços cloud](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre os limites de serviço, consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!TIP]
> Tenha em conta o `taskSlotsPerNode` valor quando constrói uma fórmula de [autoescala][enable_autoscaling] para a sua piscina. Por exemplo, uma fórmula que avalia `$RunningTasks` pode ser drasticamente afetada pelo aumento das tarefas por nó. Consulte [os nós computacional de escala automática num pool do Azure Batch](batch-automatic-scaling.md) para obter mais informações.
>
>

> [!NOTE]
> Você pode definir o `taskSlotsPerNode` elemento e a propriedade [TaskSlotsPerNode apenas][maxtasks_net] no tempo de criação da piscina. Não podem ser modificadas depois de uma piscina já ter sido criada.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de cálculo em uma piscina podem executar tarefas simultaneamente, é importante especificar como você quer que as tarefas sejam distribuídas através dos nós na piscina.

Ao utilizar a propriedade [CloudPool.TaskSchedulingPolicy,][task_schedule] pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós da piscina ("spreading"). Ou pode especificar que o maior número possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó na piscina ("embalagem").

Como exemplo de como esta funcionalidade é valiosa, considere o pool de nós [ \_ Standard D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um [valor CloudPool.TaskSlotsPerNode][maxtasks_net] de 16. Se o [CloudPool.TaskSchedulingPolicy][task_schedule] estiver configurado com um [ComputeNodeFillType][fill_type] de *Pack,* maximizaria a utilização de todos os 16 núcleos de cada nó e permitiria que uma [piscina autoscalante](batch-automatic-scaling.md) podasse os nós não usados da piscina (nós sem quaisquer tarefas atribuídas). Isto minimiza o uso de recursos e poupa dinheiro.

## <a name="variable-slots-per-task"></a>Faixas horárias variáveis por tarefa
A tarefa pode ser definida com a propriedade [CloudTask.RequiredSlots][taskslots_net] para especificar quantas faixas horárias necessita para ser executada num nó de computação, com o valor predefinido como 1. Pode definir slots de tarefas variáveis se as suas tarefas tiverem diferentes pesos no que diz respeito à utilização de recursos no nó de computação, para que cada nó de computação possa ter um número razoável de tarefas de execução simultâneas sem sobrecarregar recursos do sistema como CPU ou memória.

Por exemplo, para uma piscina com `taskSlotsPerNode = 8` propriedade, pode submeter tarefas intensivas de CPU `requiredSlots = 8` com, enquanto outras tarefas com `requiredSlots = 1` . Quando esta carga de trabalho mista estiver programada para a piscina, as tarefas intensivas do CPU serão executadas exclusivamente no nó de computação, enquanto outras tarefas podem ser executadas simultaneamente (até oito tarefas) em outros nós. Isto irá ajudá-lo a equilibrar a sua carga de trabalho através de nós computatórios e melhorar a eficiência de utilização dos recursos.

> [!TIP]
> Ao utilizar slots de tarefas variáveis, é possível que grandes tarefas com slots mais necessárias possam falhar temporariamente devido a não haver slots suficientes disponíveis em qualquer nó de computação, mesmo quando ainda há slots inativos em alguns nós. Pode aumentar a prioridade de emprego para estas tarefas para aumentar a sua possibilidade de competir por vagas disponíveis em nós.
>
> O serviço de lote também emite [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando não agenda uma tarefa a executar, enquanto continua a executar o agendamento até que as faixas horárias necessárias estejam disponíveis. Pode ouvir esse evento para detetar um potencial problema de agendamento de tarefas e fazer a sua mitigação em conformidade.
>

> [!NOTE]
> Não especifique que as tarefas `requiredSlots` são maiores do que as da `taskSlotsPerNode` piscina. Isto fará com que a tarefa nunca seja capaz de executar. Atualmente, o Serviço de Lote não faz esta validação quando submete tarefas, porque o trabalho pode não ter piscina ligada à hora de submissão, ou ser alterado para diferentes piscinas desativando/reativando.
>

## <a name="batch-net-example"></a>Exemplo lote .NET
Os seguintes snippets de código API [do Batch .NET][api_net] mostram como criar um pool com múltiplas ranhuras de tarefa por nó, e submeter a tarefa com as ranhuras necessárias.

### <a name="create-pool"></a>Criar piscina
Este código de corte mostra um pedido para criar uma piscina que contém quatro nós com quatro ranhuras de tarefa permitidas por nó. Especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó na piscina. Para obter mais informações sobre a adição de piscinas utilizando o Lote .NET API, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>Criar tarefa com slots necessários
Este corte de código cria uma tarefa com o não-padrão `requiredSlots` . Esta tarefa só será executada quando houver slots gratuitos suficientes disponíveis no nó de computação.
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Listam os nó de computação com contagens para executar tarefas e slots
Este código de snippet lista todos os nós de computação na piscina, e imprime as contagens para executar tarefas e slots de tarefas por nó.
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>A lista conta para o trabalho
Este código de corte obtém contagens de tarefas para o trabalho, que inclui tanto tarefas como slots de tarefas contam por estado de tarefa.
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Exemplo de lote REST
Este snippet API [de lote REST][api_rest] mostra um pedido para criar uma piscina que contém dois nós grandes com um máximo de quatro tarefas por nó. Para obter mais informações sobre a adição de piscinas utilizando a API REST, consulte [adicionar uma piscina a uma conta.][rest_addpool]

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

Este corte mostra um pedido para adicionar uma tarefa com não-padrão `requiredSlots` . Esta tarefa só será executada quando houver slots gratuitos suficientes disponíveis no nó de computação.
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra a utilização da propriedade [CloudPool.TaskSlotsPerNode.][maxtasks_net]

Esta aplicação de consola C# utiliza a biblioteca [Batch .NET][api_net] para criar uma piscina com um ou mais nós de computação. Executa um número configurável de tarefas nesses nós para simular carga variável. A saída da aplicação especifica quais os nós executados em cada tarefa. A aplicação também fornece um resumo dos parâmetros e duração do trabalho. A parte sumária da saída de dois percursos diferentes da aplicação da amostra aparece abaixo.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução da aplicação da amostra mostra que com um único nó na piscina e a definição padrão de uma tarefa por nó, a duração do trabalho é superior a 30 minutos.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda parte da amostra revela uma diminuição significativa da duração do trabalho. Isto porque a piscina foi configurada com quatro tarefas por nó, o que permite a execução paralela da tarefa para completar o trabalho em quase um quarto do tempo.

> [!NOTE]
> As durações do trabalho nos resumos acima não incluem o tempo de criação da piscina. Cada um dos postos de trabalho acima foi submetido a piscinas previamente criadas cujos nós computacional estavam no estado *de Idle* no momento da submissão.
>
>

## <a name="next-steps"></a>Passos seguintes
### <a name="batch-explorer-heat-map"></a>Mapa de calor do explorador de lote
[O Batch Explorer][batch_labs] é uma ferramenta cliente independente e independente, com destaque para ajudar a criar, depurar e monitorizar as aplicações do Azure Batch. O Batch Explorer contém uma funcionalidade *de Mapa de Calor* que fornece visualização da execução de tarefas. Ao executar a aplicação de amostra [ParallelTasks,][parallel_tasks_sample] pode utilizar a funcionalidade Heat Map para visualizar facilmente a execução de tarefas paralelas em cada nó.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
