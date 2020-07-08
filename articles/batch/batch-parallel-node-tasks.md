---
title: Executar tarefas paralelamente para otimizar recursos computacional
description: Aumentar a eficiência e reduzir os custos utilizando menos nós de computação e executando tarefas simultâneas em cada nó num pool do Azure Batch
ms.topic: how-to
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1b13f7f276740cd4f37e8d4c4ba1f2967d919ccf
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961579"
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
Configura os nós computacional para execução paralela de tarefas ao nível da piscina. Com a biblioteca Batch .NET, desaperte a propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net] quando criar uma piscina. Se estiver a utilizar a API de Lote REST, desaperte o elemento [maxTasksPerNode][rest_addpool] no corpo pedido durante a criação da piscina.

O Azure Batch permite-lhe definir tarefas por nó até (4x) o número de nós de núcleo. Por exemplo, se a piscina estiver configurada com nós de tamanho "Grande" (quatro núcleos), `maxTasksPerNode` pode ser configurado para 16. No entanto, independentemente de quantos núcleos o nó tem, não pode ter mais de 256 tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos dos nós, consulte [tamanhos para serviços cloud](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre os limites de serviço, consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!TIP]
> Tenha em conta o `maxTasksPerNode` valor quando constrói uma fórmula de [autoescala][enable_autoscaling] para a sua piscina. Por exemplo, uma fórmula que avalia `$RunningTasks` pode ser drasticamente afetada pelo aumento das tarefas por nó. Consulte [os nós computacional de escala automática num pool do Azure Batch](batch-automatic-scaling.md) para obter mais informações.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de cálculo em uma piscina podem executar tarefas simultaneamente, é importante especificar como você quer que as tarefas sejam distribuídas através dos nós na piscina.

Ao utilizar a propriedade [CloudPool.TaskSchedulingPolicy,][task_schedule] pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós da piscina ("spreading"). Ou pode especificar que o maior número possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó na piscina ("embalagem").

Como exemplo de como esta funcionalidade é valiosa, considere o pool de nós [ \_ Standard D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Se o [CloudPool.TaskSchedulingPolicy][task_schedule] estiver configurado com um [ComputeNodeFillType][fill_type] de *Pack,* maximizaria a utilização de todos os 16 núcleos de cada nó e permitiria que uma [piscina autoscalante](batch-automatic-scaling.md) podasse os nós não usados da piscina (nós sem quaisquer tarefas atribuídas). Isto minimiza o uso de recursos e poupa dinheiro.

## <a name="batch-net-example"></a>Exemplo lote .NET
Este corte de código API [do Lote .NET][api_net] mostra um pedido para criar uma piscina que contenha quatro nós com um máximo de quatro tarefas por nó. Especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó na piscina. Para obter mais informações sobre a adição de piscinas utilizando o Lote .NET API, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
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
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Você pode definir o `maxTasksPerNode` elemento e [maxTasksPerComputeNode][maxtasks_net] propriedade apenas no tempo de criação da piscina. Não podem ser modificadas depois de uma piscina já ter sido criada.
>
>

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra a utilização da propriedade [CloudPool.MaxTasksPerComputeNode.][maxtasks_net]

Esta aplicação de consola C# utiliza a biblioteca [Batch .NET][api_net] para criar uma piscina com um ou mais nós de computação. Executa um número configurável de tarefas nesses nós para simular carga variável. A saída da aplicação especifica quais os nós executados em cada tarefa. A aplicação também fornece um resumo dos parâmetros e duração do trabalho. A parte sumária da saída de dois percursos diferentes da aplicação da amostra aparece abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução da aplicação da amostra mostra que com um único nó na piscina e a definição padrão de uma tarefa por nó, a duração do trabalho é superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda parte da amostra revela uma diminuição significativa da duração do trabalho. Isto porque a piscina foi configurada com quatro tarefas por nó, o que permite a execução paralela da tarefa para completar o trabalho em quase um quarto do tempo.

> [!NOTE]
> As durações do trabalho nos resumos acima não incluem o tempo de criação da piscina. Cada um dos postos de trabalho acima foi submetido a piscinas previamente criadas cujos nós computacional estavam no estado *de Idle* no momento da submissão.
>
>

## <a name="next-steps"></a>Próximos passos
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

