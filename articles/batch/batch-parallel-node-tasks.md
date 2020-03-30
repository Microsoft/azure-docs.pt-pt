---
title: Executar tarefas paralelas à otimização de recursos computacionais - Lote Azure
description: Aumentar a eficiência e reduzir os custos utilizando menos nós de cálculo e executando tarefas simultâneas em cada nó numa piscina de Lote Azure
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023638"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Executar tarefas simultaneamente para maximizar o uso de nós computacionais de Lote 

Executando mais de uma tarefa simultaneamente em cada nó de computação na sua piscina de Lote Azure, você pode maximizar o uso de recursos em um número menor de nós na piscina. Para algumas cargas de trabalho, isto pode resultar em tempos de trabalho mais curtos e custos mais baixos.

Embora alguns cenários beneficiem de dedicar todos os recursos de um nó a uma única tarefa, várias situações beneficiam de permitir que múltiplas tarefas partilhem esses recursos:

* **Minimizar a transferência de dados** quando as tarefas são capazes de partilhar dados. Neste cenário, pode reduzir drasticamente os encargos de transferência de dados copiando dados partilhados para um número menor de nós e executando tarefas paralelamente em cada nó. Isto aplica-se especialmente se os dados a copiar para cada nó devem ser transferidos entre regiões geográficas.
* **Maximizar o uso** da memória quando as tarefas requerem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo, e em momentos variáveis durante a execução. Você pode empregar menos, mas maiores, nós de computação com mais memória para lidar eficientemente com tais picos. Estes nós teriam múltiplas tarefas em paralelo em cada nó, mas cada tarefa aproveitaria a memória abundante dos nós em diferentes momentos.
* **Atenuando os limites** do número do nó quando a comunicação inter-nó é necessária dentro de uma piscina. Atualmente, as piscinas configuradas para comunicação inter-nó estão limitadas a 50 nós de computação. Se cada nó numa piscina deste tipo for capaz de executar tarefas paralelamente, um maior número de tarefas pode ser executada simultaneamente.
* **Replicando um cluster de cálculo no local,** como quando se move pela primeira vez um ambiente de computação para Azure. Se a sua solução atual no local executar múltiplas tarefas por nó de cálculo, pode aumentar o número máximo de tarefas do nó para espelhar mais de perto essa configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Como exemplo para ilustrar os benefícios da execução paralela da tarefa, digamos que a sua aplicação de tarefa tem CPU e requisitos de memória de tal forma que os nós [\_Standard D1](../cloud-services/cloud-services-sizes-specs.md) são suficientes. Mas, para terminar o trabalho no tempo necessário, são necessários 1.000 destes nós.

Em vez\_de utilizar os nós Standard D1 que têm 1 núcleo CPU, pode utilizar nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) que têm 16 núcleos cada, e permitir a execução paralela de tarefas. Portanto, *16 vezes menos nós* poderiam ser usados - em vez de 1.000 nós, apenas 63 seriam necessários. Além disso, se forem necessários grandes ficheiros de aplicação ou dados de referência para cada nó, a duração do trabalho e a eficiência voltarão a melhorar, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Permitir a execução paralela de tarefas
Configura ruma a computação para execução de tarefaparalela ao nível da piscina. Com a biblioteca Batch .NET, delineie a propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net] quando criar uma piscina. Se estiver a utilizar a API do Lote REST, defino o elemento [maxTasksPerNode][rest_addpool] no organismo de pedido durante a criação da piscina.

O Lote Azure permite-lhe definir tarefas por nó até (4x) o número de nós principais. Por exemplo, se a piscina estiver configurada com nós de tamanho `maxTasksPerNode` "Grande" (quatro núcleos), então pode ser definido para 16. No entanto, independentemente de quantos núcleos o nó tem, não pode ter mais de 256 tarefas por nó. Para mais detalhes sobre o número de núcleos para cada um dos tamanhos do nó, consulte [Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre os limites de serviço, consulte [Quotas e limites para o serviço De lote Azure](batch-quota-limit.md).

> [!TIP]
> Certifique-se de ter `maxTasksPerNode` em conta o valor quando construir uma fórmula de [escala automática][enable_autoscaling] para a sua piscina. Por exemplo, uma fórmula `$RunningTasks` que avalia poderia ser drasticamente afetada por um aumento de tarefas por nó. Consulte [automaticamente os nós de computação numa piscina de Lote Azure](batch-automatic-scaling.md) para obter mais informações.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós computacionais numa piscina podem executar tarefas simultaneamente, é importante especificar como pretende que as tarefas sejam distribuídas pelos nós da piscina.

Ao utilizar a propriedade [CloudPool.TaskSchedulingPolicy,][task_schedule] pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós da piscina ("spreading"). Ou pode especificar que o maior número possível de tarefas deve ser atribuída a cada nó antes de as tarefas serem atribuídas a outro nó na piscina ("embalagem").

Como exemplo de como esta funcionalidade é valiosa, considere o conjunto de nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Se a [CloudPool.TaskSchedulingPolicy][task_schedule] estiver configurada com um [ComputeNodeFillType][fill_type] of *Pack,* maximizaria a utilização de todos os 16 núcleos de cada nó e permitiria que uma [piscina autoscalcificada](batch-automatic-scaling.md) podasse nós não utilizados da piscina (nós sem tarefas atribuídas). Isto minimiza o uso de recursos e poupa dinheiro.

## <a name="batch-net-example"></a>Lote .NET exemplo
Este código [De lote .NET][api_net] API mostra um pedido para criar uma piscina que contenha quatro nós com um máximo de quatro tarefas por nó. Especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó na piscina. Para obter mais informações sobre a adição de piscinas utilizando o Batch .NET API, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Exemplo de REPOUSO de Lote
Este snippet API [de repouso de lote][api_rest] mostra um pedido para criar uma piscina que contém dois grandes nós com um máximo de quatro tarefas por nó. Para obter mais informações sobre a adição de piscinas utilizando a API REST, consulte [Adicionar uma piscina a uma conta][rest_addpool].

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
> Você pode `maxTasksPerNode` definir o elemento e propriedade [MaxTasksPerComputeNode][maxtasks_net] apenas no tempo de criação da piscina. Não podem ser modificados depois de já ter sido criada uma piscina.
>
>

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra a utilização da propriedade [CloudPool.MaxTasksPerComputeNode.][maxtasks_net]

Esta aplicação de consola C# utiliza a biblioteca [Batch .NET][api_net] para criar uma piscina com um ou mais nós de computação. Executa um número configurável de tarefas nesses nós para simular a carga variável. A saída da aplicação especifica quais os nós executados cada tarefa. O pedido também fornece um resumo dos parâmetros de trabalho e duração. A parte sumária da saída de duas diferentes execuções da aplicação da amostra aparece abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução da aplicação da amostra mostra que, com um único nó na piscina e a definição padrão de uma tarefa por nó, a duração do trabalho é superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda série da amostra revela uma diminuição significativa da duração do trabalho. Isto porque a piscina foi configurada com quatro tarefas por nó, o que permite a execução paralela de tarefas para completar o trabalho em quase um quarto do tempo.

> [!NOTE]
> As durações de trabalho nos resumos acima não incluem o tempo de criação de piscinas. Cada um dos trabalhos acima foi submetido a piscinas previamente criadas cujos nós de computação estavam no estado *de Idle* no momento da submissão.
>
>

## <a name="next-steps"></a>Passos seguintes
### <a name="batch-explorer-heat-map"></a>Mapa de calor do explorador do lote
[O Batch Explorer][batch_labs] é uma ferramenta de cliente gratuita, rica e autónoma para ajudar a criar, depurar e monitorizar aplicações do Lote Azure. O Batch Explorer contém uma funcionalidade *de Mapa* de Calor que proporciona visualização da execução da tarefa. Ao executar a aplicação da amostra [ParallelTasks,][parallel_tasks_sample] pode utilizar a função Heat Map para visualizar facilmente a execução de tarefas paralelas em cada nó.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

