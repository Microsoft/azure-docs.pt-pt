---
title: Executar tarefas paralelas à otimização de recursos computacionais - Lote Azure
description: Aumente a eficiência e reduza os custos usando menos nós de computação e executando tarefas simultâneas em cada nó em um pool do lote do Azure
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ee0db85818349938fcf5248e10e5eaac546bae5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930441"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Executar tarefas simultaneamente para maximizar o uso de nós de computação do lote 

Executando mais de uma tarefa simultaneamente em cada nó de computação em seu pool do lote do Azure, você pode maximizar o uso de recursos em um número menor de nós no pool. Para algumas cargas de trabalho, isso pode resultar em tempos de trabalhos menores e menor custo.

Embora alguns cenários se beneficiem de dedicar todos os recursos de um nó a uma única tarefa, várias situações se beneficiam de permitir que várias tarefas compartilhem esses recursos:

* **Minimizar a transferência de dados** quando as tarefas são capazes de compartilhar dados. Nesse cenário, você pode reduzir drasticamente os encargos de transferência de dados copiando dados compartilhados para um número menor de nós e executando tarefas em paralelo em cada nó. Isso se aplica especialmente se os dados a serem copiados para cada nó devem ser transferidos entre regiões geográficas.
* **Maximizar o uso de memória** quando as tarefas exigem uma grande quantidade de memória, mas somente durante curtos períodos de tempo e em momentos variáveis durante a execução. Você pode empregar nós de computação menores, mas maiores, com mais memória para lidar com eficiência com esses picos. Esses nós teriam várias tarefas em execução em paralelo em cada nó, mas cada tarefa aproveitaria a memória numerosas dos nós em momentos diferentes.
* **Redução dos limites de número de nós** quando a comunicação entre nós é necessária em um pool. Atualmente, os pools configurados para comunicação entre nós são limitados a nós de computação 50. Se cada nó em um pool for capaz de executar tarefas em paralelo, um número maior de tarefas poderá ser executado simultaneamente.
* **Replicar um cluster de computação local**, como quando você move um ambiente de computação pela primeira vez para o Azure. Se sua solução local atual executar várias tarefas por nó de computação, você poderá aumentar o número máximo de tarefas de nó para espelhar mais de forma mais próxima essa configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Como exemplo para ilustrar os benefícios da execução paralela da tarefa, digamos que a sua aplicação de tarefa tem CPU e requisitos de memória tais que os nós [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) são suficientes. Mas, para concluir o trabalho no tempo necessário, 1.000 desses nós são necessários.

Em vez de utilizar os nós Standard\_D1 que têm 1 núcleo CPU, pode utilizar os nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) que têm 16 núcleos cada, e permitir a execução paralela de tarefas. Portanto, *16 vezes menos nós* poderiam ser usados, em vez de 1.000 nós, apenas 63 seria necessário. Além disso, se arquivos de aplicativo grandes ou dados de referência forem necessários para cada nó, a duração e a eficiência do trabalho serão novamente aprimoradas, já que os dados são copiados para apenas nós 63.

## <a name="enable-parallel-task-execution"></a>Habilitar a execução de tarefas paralelas
Você configura nós de computação para a execução de tarefas paralelas no nível do pool. Com a biblioteca .NET do lote, defina a propriedade [CloudPool. MaxTasksPerComputeNode][maxtasks_net] ao criar um pool. Se você estiver usando a API REST do lote, defina o elemento [maxTasksPerNode][rest_addpool] no corpo da solicitação durante a criação do pool.

O lote do Azure permite que você defina tarefas por nó até (4x) o número de nós principais. Por exemplo, se o pool estiver configurado com nós de tamanho "grande" (quatro núcleos), `maxTasksPerNode` poderá ser definido como 16. No entanto, independentemente de quantos núcleos o nó tem, você não pode ter mais de 256 tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, consulte [tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre limites de serviço, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md).

> [!TIP]
> Lembre-se de levar em conta o valor de `maxTasksPerNode` ao construir uma [fórmula de dimensionamento automático][enable_autoscaling] para o pool. Por exemplo, uma fórmula que avalia `$RunningTasks` pode ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [dimensionar automaticamente nós de computação em um pool do lote do Azure](batch-automatic-scaling.md) para obter mais informações.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação em um pool podem executar tarefas simultaneamente, é importante especificar como você deseja que as tarefas sejam distribuídas entre os nós no pool.

Usando a propriedade [CloudPool. TaskSchedulingPolicy][task_schedule] , você pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo de tarefas possível deve ser atribuído a cada nó antes que as tarefas sejam atribuídas a outro nó no pool ("empacotamento").

Como exemplo de como esta funcionalidade é valiosa, considere o conjunto de nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) que está configurado com um valor [CloudPool.MaxTasksPerComputeNode][maxtasks_net] de 16. Se o [CloudPool. TaskSchedulingPolicy][task_schedule] for configurado com um [ComputeNodeFillType][fill_type] do *Pack*, ele maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento](batch-automatic-scaling.md) automático removesse nós não utilizados do pool (nós sem nenhuma tarefa atribuída). Isso minimiza o uso de recursos e economiza dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET do lote
Este trecho de código de API [.net do lote][api_net] mostra uma solicitação para criar um pool que contém quatro nós com um máximo de quatro tarefas por nó. Ele especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no pool. Para obter mais informações sobre como adicionar pools usando a API .NET do lote, consulte [BatchClient. PoolOperations. createpool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Exemplo de REST do lote
Este trecho da API [REST do lote][api_rest] mostra uma solicitação para criar um pool que contém dois nós grandes com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta][rest_addpool].

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
> Você pode definir o elemento `maxTasksPerNode` e a propriedade [MaxTasksPerComputeNode][maxtasks_net] somente no momento da criação do pool. Eles não podem ser modificados depois que um pool já tiver sido criado.
>
>

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra o uso da propriedade [CloudPool. MaxTasksPerComputeNode][maxtasks_net] .

Esse C# aplicativo de console usa a biblioteca [.net do lote][api_net] para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular a carga da variável. A saída do aplicativo especifica quais nós executaram cada tarefa. O aplicativo também fornece um resumo dos parâmetros e da duração do trabalho. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo aparece abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que, com um único nó no pool e a configuração padrão de uma tarefa por nó, a duração do trabalho é de mais de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma diminuição significativa na duração do trabalho. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, o que permite que a execução de tarefa paralela conclua o trabalho em quase um trimestre do tempo.

> [!NOTE]
> As durações de trabalho nos resumos acima não incluem o tempo de criação do pool. Cada um dos trabalhos acima foi enviado para os pools criados anteriormente cujos nós de computação estavam no estado *ocioso* no momento do envio.
>
>

## <a name="next-steps"></a>Passos seguintes
### <a name="batch-explorer-heat-map"></a>Mapa de calor Batch Explorer
[Batch Explorer][batch_labs] é uma ferramenta de cliente autônoma, gratuita e com recursos avançados para ajudar a criar, depurar e monitorar aplicativos do lote do Azure. Batch Explorer contém um recurso de *mapa de calor* que fornece visualização da execução da tarefa. Quando estiver executando o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample] , você poderá usar o recurso de mapa de calor para visualizar facilmente a execução de tarefas paralelas em cada nó.


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

