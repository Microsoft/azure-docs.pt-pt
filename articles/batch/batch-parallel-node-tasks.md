---
title: Executar tarefas simultaneamente para maximizar o uso de nó de computação batch
description: Aumentar a eficiência e reduzir os custos utilizando menos nós de computação e tarefas de execução paralelas a cada nó num pool do Azure Batch
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102970"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Executar tarefas simultaneamente para maximizar o uso de nó de computação batch

Pode maximizar o uso de recursos num número menor de nós de computação na sua piscina executando mais do que uma tarefa simultaneamente em cada nó.

Embora alguns cenários funcionem melhor com todos os recursos de um nó dedicados a uma única tarefa, certas cargas de trabalho podem ver tempos de trabalho mais curtos e custos mais baixos quando várias tarefas partilham esses recursos:

- **Minimizar a transferência de dados** para tarefas que sejam capazes de partilhar dados. Pode reduzir drasticamente os encargos de transferência de dados copiando dados partilhados para um número menor de nós e executando tarefas paralelamente em cada nó. Isto aplica-se especialmente se os dados a copiar para cada nó tão devem ser transferidos entre regiões geográficas.
- **Maximize o uso** da memória para tarefas que requerem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo, e em tempos variáveis durante a execução. Pode empregar menos, mas maiores, nós computacional com mais memória para lidar eficientemente com esses picos. Estes nós teriam múltiplas tarefas em paralelo em cada nó, mas cada tarefa tiraria partido da memória abundante dos nós em diferentes momentos.
- **Atenuar os limites do número do nó** quando for necessária uma comunicação inter-node dentro de uma piscina. Atualmente, as piscinas configuradas para a comunicação entre nós estão limitadas a 50 nós computacional. Se cada nó em tal piscina for capaz de executar tarefas em paralelo, um maior número de tarefas pode ser executado simultaneamente.
- **Replique um cluster compute no local,** como quando move um ambiente computacional para Azure. Se a sua solução atual no local executar múltiplas tarefas por nó de computação, pode aumentar o número máximo de tarefas de nó para espelhar mais de perto essa configuração.

## <a name="example-scenario"></a>Cenário de exemplo

Como exemplo, imagine uma aplicação de tarefa com CPU e requisitos de memória de modo a que os nós [ \_ Standard D1](../cloud-services/cloud-services-sizes-specs.md) sejam suficientes. No entanto, para terminar o trabalho no tempo necessário, são necessários 1.000 destes nós.

Em vez de utilizar \_ nós Standard D1 que tenham 1 núcleo CPU, pode utilizar nós [Standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) que têm 16 núcleos cada, e permitir a execução paralela da tarefa. Isto significa que *16 vezes menos nós* poderiam ser usados -- em vez de 1.000 nós, apenas 63 seriam necessários. Se forem necessários ficheiros de aplicações grandes ou dados de referência para cada nó, a duração e eficiência do trabalho são novamente melhoradas, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Permitir a execução paralela de tarefas

Configura os nós computacional para execução paralela de tarefas ao nível da piscina. Com a biblioteca Batch .NET, deslove a propriedade [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) quando criar uma piscina. Se estiver a utilizar a API de Lote REST, desloque as [tarefasSlotsPerNode](/rest/api/batchservice/pool/add) elemento no corpo de pedido durante a criação da piscina.

> [!NOTE]
> Você pode definir o `taskSlotsPerNode` elemento e a propriedade [TaskSlotsPerNode apenas](/dotnet/api/microsoft.azure.batch.cloudpool) no tempo de criação da piscina. Não podem ser modificados depois de uma piscina já ter sido criada.

O Azure Batch permite-lhe definir ranhuras de tarefa por nó até (4x) o número de núcleos de nó. Por exemplo, se a piscina estiver configurada com nós de tamanho "Grande" (quatro núcleos), `taskSlotsPerNode` pode ser configurado para 16. No entanto, independentemente de quantos núcleos o nó tem, não pode ter mais de 256 slots de tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos dos nós, consulte [tamanhos para serviços cloud](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre os limites de serviço, consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!TIP]
> Tenha em conta o `taskSlotsPerNode` valor quando constrói uma fórmula de [autoescala](/rest/api/batchservice/pool/enableautoscale) para a sua piscina. Por exemplo, uma fórmula que avalia `$RunningTasks` pode ser drasticamente afetada pelo aumento das tarefas por nó. Para obter mais informações, consulte [os nós computacional de escala automática numa piscina Azure Batch](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Especificar a distribuição de tarefas

Ao ativar tarefas simultâneas, é importante especificar como pretende que as tarefas sejam distribuídas pelos nós da piscina.

Ao utilizar a propriedade [CloudPool.TaskSchedulingPolicy,](/dotnet/api/microsoft.azure.batch.cloudpool) pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós da piscina ("spreading"). Ou pode especificar que o maior número possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó na piscina ("embalagem").

Como exemplo, considere o pool de nós [ \_ Standard D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um valor [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) de 16. Se o [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) estiver configurado com um [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) de *Pack,* maximizaria a utilização de todos os 16 núcleos de cada nó e permitiria que uma [piscina auto-caling](batch-automatic-scaling.md) removesse nós não usados (nós sem quaisquer tarefas atribuídas) da piscina. Isto minimiza o uso de recursos e poupa dinheiro.

## <a name="define-variable-slots-per-task"></a>Definir faixas variáveis por tarefa

Uma tarefa pode ser definida com a propriedade [CloudTask.RequiredSlots,](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) especificando quantas faixas horárias necessita para ser executada num nó de computação. O valor predefinido como 1. Pode definir slots de tarefas variáveis se as suas tarefas tiverem diferentes pesos no que diz respeito à utilização de recursos no nó de computação. Isto permite que cada nó de cálculo tenha um número razoável de tarefas de execução simultâneas sem sobrecarregar recursos do sistema como CPU ou memória.

Por exemplo, para uma piscina com `taskSlotsPerNode = 8` propriedade, pode submeter tarefas intensivas de CPU multi-core `requiredSlots = 8` com, enquanto outras tarefas podem ser definidas para `requiredSlots = 1` . Quando esta carga de trabalho mista é programada, as tarefas intensivas do CPU serão executadas exclusivamente nos seus nós de computação, enquanto outras tarefas podem ser executadas simultaneamente (até oito tarefas ao mesmo tempo) em outros nós. Isto ajuda-o a equilibrar a sua carga de trabalho através dos nós de cálculo e a melhorar a eficiência de utilização dos recursos.

> [!TIP]
> Ao utilizar slots de tarefas variáveis, é possível que grandes tarefas com slots mais necessárias possam falhar temporariamente, porque não há slots suficientes em qualquer nó de computação, mesmo quando ainda há slots inativos em alguns nós. Pode aumentar a prioridade de emprego para estas tarefas para aumentar a sua possibilidade de competir por vagas disponíveis em nós.
>
> O serviço Batch emite o [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando não agenda uma tarefa a executar, e continua a executar o agendamento até que as faixas horárias necessárias estejam disponíveis. Pode ouvir esse evento para detetar potenciais problemas de agendamento de tarefas e mitigar em conformidade.

> [!NOTE]
> Não especifique que uma tarefa `requiredSlots` seja maior do que a da `taskSlotsPerNode` piscina. Isto resultará em que a tarefa nunca seja capaz de executar. O Serviço de Lote não valida atualmente este conflito quando submete tarefas porque um trabalho pode não ter uma piscina ligada à hora de submissão, ou pode ser alterado para um pool diferente, desativando/re-habilitando.

## <a name="batch-net-example"></a>Exemplo lote .NET

Os seguintes snippets de código API [do Batch .NET](/dotnet/api/microsoft.azure.batch) mostram como criar um pool com múltiplas ranhuras de tarefa por nó e como submeter uma tarefa com as ranhuras necessárias.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Criar uma piscina com múltiplas ranhuras de tarefa por nó

Este código de corte mostra um pedido para criar uma piscina que contém quatro nós, com quatro ranhuras de tarefa permitidas por nó. Especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó na piscina.

Para obter mais informações sobre a adição de piscinas utilizando o Lote .NET API, consulte [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations).

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

### <a name="create-a-task-with-required-slots"></a>Criar uma tarefa com slots necessários

Este corte de código cria uma tarefa com o não-padrão `requiredSlots` . Esta tarefa só será executada quando houver slots gratuitos suficientes disponíveis num nó de computação.

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

Este código de corte obtém contagens de tarefas para o trabalho, que inclui tanto tarefas como slots de tarefas contando por estado de tarefa.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Exemplo de lote REST

Os seguintes snippets de código API [do Lote REST](/rest/api/batchservice/) mostram como criar um pool com múltiplas ranhuras de tarefa por nó e como submeter uma tarefa com as ranhuras necessárias.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Criar uma piscina com múltiplas ranhuras de tarefa por nó

Este corte mostra um pedido para criar uma piscina que contém dois nós grandes com um máximo de quatro tarefas por nó.

Para obter mais informações sobre a adição de piscinas utilizando a API REST, consulte [adicionar uma piscina a uma conta.](/rest/api/batchservice/pool/add)

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

### <a name="create-a-task-with-required-slots"></a>Criar uma tarefa com slots necessários

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

## <a name="code-sample-on-github"></a>Amostra de código no GitHub

O projeto [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) no GitHub ilustra a utilização da propriedade [CloudPool.TaskSlotsPerNode.](/dotnet/api/microsoft.azure.batch.cloudpool)

Esta aplicação de consola C# utiliza a biblioteca [Batch .NET](/dotnet/api/microsoft.azure.batch) para criar uma piscina com um ou mais nós de computação. Executa um número configurável de tarefas nesses nós para simular uma carga variável. A saída da aplicação mostra quais os nós executados em cada tarefa. A aplicação também fornece um resumo dos parâmetros e duração do trabalho.

Como exemplo, abaixo está a parte sumária da saída de dois percursos diferentes da aplicação da amostra ParallelTasks. As durações do trabalho mostradas aqui não incluem o tempo de criação de piscina, uma vez que cada trabalho foi submetido a um pool previamente criado cujos nós computativos estavam no estado *de Idle* na hora de submissão.

A primeira execução da aplicação da amostra mostra que com um único nó na piscina e a definição padrão de uma tarefa por nó, a duração do trabalho é superior a 30 minutos.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A segunda parte da amostra revela uma diminuição significativa da duração do trabalho. Isto porque a piscina foi configurada com quatro tarefas por nó, permitindo que a execução paralela da tarefa completasse o trabalho em quase um quarto do tempo.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Passos seguintes

- Experimente o mapa de calor [do Explorador do Lote.](https://azure.github.io/BatchExplorer/) O Batch Explorer é uma ferramenta cliente independente e independente, com destaque para ajudar a criar, depurar e monitorizar as aplicações do Azure Batch. Ao executar a aplicação de amostra [ParallelTasks,](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) a funcionalidade Mapa de Calor do Explorador de Lote permite visualizar facilmente a execução de tarefas paralelas em cada nó.
- Explore [as amostras do Azure Batch no GitHub.](https://github.com/Azure/azure-batch-samples)
- Saiba mais sobre [as dependências de tarefas do Batch.](batch-task-dependencies.md)
