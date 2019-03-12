---
title: Executar tarefas em paralelo para usar recursos de computação com eficiência - Azure Batch | Documentos da Microsoft
description: Aumentar a eficiência e reduzir os custos com menos nós de computação e tarefas em execução em simultâneo em cada nó num conjunto do Azure Batch
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583ccb6076dae2f33e265b95387bcd35aa9fa4d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547287"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Nós de computação de execução de tarefas em simultâneo para maximizar a utilização do Batch 

Ao executar mais de uma tarefa em simultâneo em cada nó de computação no conjunto do Azure Batch, pode maximizar a utilização de recursos num número menor de nós no conjunto. Para algumas cargas de trabalho, isso pode resultar em tempos mais curtos de tarefa e reduzir os custos.

Embora alguns cenários se beneficiar de dedicar todos os recursos de um nó para uma única tarefa, várias situações beneficiam-se de que permite que várias tarefas compartilhar esses recursos:

* **Minimizar a transferência de dados** quando as tarefas são capazes de partilhar dados. Neste cenário, é possível reduzir drasticamente os custos de transferência de dados ao copiar dados partilhados para um número menor de nós e execução de tarefas em paralelo em cada nó. Isto aplica-se especialmente se os dados para ser copiado para cada nó têm de ser transferidos entre regiões geográficas.
* **Maximizando o uso de memória** quando tarefas exigem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo e, em momentos de variáveis durante a execução. Pode utilizar nós de computação de menos, mas maior, com mais memória com eficiência lidar com esses picos. Estes nós teria várias tarefas em execução em paralelo em cada nó, mas cada tarefa seria a tirar partido de memória de numerosas de nós em momentos diferentes.
* **Mitigar os limites de número de nó** quando a comunicação entre nós é necessária dentro de um conjunto. Atualmente, os agrupamentos configurados para comunicação entre nós estão limitados a 50 nós de computação. Se cada nó tal um conjunto é capaz de executar tarefas em paralelo, um maior número de tarefas pode ser executado em simultâneo.
* **Replicar um cluster de computação no local**, por exemplo, quando primeiro move um ambiente de computação para o Azure. Se a sua solução no local atual executa várias tarefas por nó de computação, pode aumentar o número máximo de tarefas de nós para espelhar mais de perto o que a configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Como exemplo para ilustrar os benefícios da execução de tarefas paralelas, vamos supor que a aplicação de tarefa tem requisitos de CPU e memória, de modo que [padrão\_D1](../cloud-services/cloud-services-sizes-specs.md) nós são suficientes. No entanto, para concluir a tarefa no tempo necessário, 1.000 destes nós são necessários.

Em vez de usar o padrão\_nós D1 com 1 núcleo de CPU, poderia usar [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md) nós que tem 16 núcleos e ativar a execução de tarefas paralelas. Por conseguinte, *16 vezes menos nós* poderia ser usado – em vez de 1000 nós, só 63 seria necessário. Além disso, se os ficheiros de aplicação grandes ou dados de referência são necessários para cada nó, duração de tarefa e a eficiência são novamente melhorados, uma vez que os dados são copiados para nós apenas 63.

## <a name="enable-parallel-task-execution"></a>Ativar a execução de tarefas paralelas
Configurar nós de computação para a execução da tarefa paralela ao nível do conjunto. Com a biblioteca .NET do Batch, definir o [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade quando cria um conjunto. Se estiver a utilizar a API de REST do Batch, defina o [maxTasksPerNode] [ rest_addpool] elemento no corpo do pedido durante a criação do conjunto.

O Azure Batch permite-lhe definir tarefas máximas por nó até quatro vezes (4 x) o número de núcleos do nó. Por exemplo, se o conjunto está configurado connosco de tamanho "Large" (quatro núcleos), em seguida, `maxTasksPerNode` pode ser definido como 16. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, veja [tamanhos para os serviços Cloud](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre limites de serviço, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!TIP]
> Certifique-se de que levar em conta a `maxTasksPerNode` valor ao construir um [fórmula de dimensionamento automático] [ enable_autoscaling] para o seu conjunto. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetados por um aumento de tarefas por nó. Ver [nós num conjunto do Azure Batch de computação de dimensionamento de automaticamente](batch-automatic-scaling.md) para obter mais informações.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação num conjunto podem executar tarefas em simultâneo, é importante especificar como pretende que as tarefas sejam distribuídos entre os nós no conjunto.

Ao utilizar o [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriedade, pode especificar que devem ser atribuídas tarefas uniformemente em todos os nós no conjunto ("propagação"). Em alternativa, pode especificar que todas as tarefas possível devem ser atribuídas a cada nó antes de lhe sejam atribuídas tarefas para outro nó no conjunto ("Remessa").

Como um exemplo de como esta funcionalidade é útil, considere o conjunto de [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md) nós (no exemplo acima), que está configurado com um [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valor de 16. Se o [CloudPool.TaskSchedulingPolicy] [ task_schedule] está configurado com um [ComputeNodeFillType] [ fill_type] de *pacote*, seria maximizar a utilização de todos os 16 núcleos de cada nó e permitir que um [conjunto de dimensionamento automático](batch-automatic-scaling.md) para eliminar nós não utilizadas do conjunto (nós sem quaisquer tarefas atribuídas). Isso minimiza a utilização de recursos e poupa dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET do batch
Isso [.NET do Batch] [ api_net] trecho de código de API mostra uma solicitação para criar um conjunto que contém quatro nós com um máximo de quatro tarefas por nó. Especifica uma política que irá preencher cada nó com tarefas antes de atribuir tarefas a outro nó no conjunto de agendamento de tarefas. Para obter mais informações sobre como adicionar agrupamentos, utilizando a API .NET do Batch, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

## <a name="batch-rest-example"></a>Exemplo REST do batch
Isso [REST do Batch] [ api_rest] trecho de API mostra uma solicitação para criar um conjunto que contém dois nós de grandes dimensões com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar conjuntos com a API REST, consulte [adicionar um conjunto a uma conta][rest_addpool].

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
> Pode definir o `maxTasksPerNode` elemento e [MaxTasksPerComputeNode] [ maxtasks_net] propriedade apenas no momento da criação de conjunto. Não pode ser modificados depois de um agrupamento já foi criado.
>
>

## <a name="code-sample"></a>Exemplo de código
O [ParallelNodeTasks] [ parallel_tasks_sample] projeto no GitHub ilustra o uso do [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade.

Esta aplicação de consola c# utiliza a [.NET do Batch] [ api_net] biblioteca para criar um conjunto com um ou mais nós de computação. Executa um número configurável de tarefas em nós para simular carga variável. Resultados da aplicação especifica quais os nós executou cada tarefa. O aplicativo também fornece um resumo dos parâmetros da tarefa e duração. A parte de resumida da saída de duas execuções diferentes do exemplo de aplicação é apresentada abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que com um único nó no conjunto e a predefinição de uma tarefa por nó, a duração de tarefa é mais de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda executada do exemplo mostra uma queda considerável na duração de tarefa. Isto acontece porque o conjunto foi configurado com quatro tarefas por nó, o que permite a execução da tarefa paralela concluir a tarefa em quase um trimestre do tempo.

> [!NOTE]
> As durações de tarefa em resumos acima não incluem o tempo de criação de conjunto. Cada uma das tarefas acima foi submetida para criado anteriormente conjuntos cujos nós de computação foram no *Idle* Estado em tempo de envio.
>
>

## <a name="next-steps"></a>Passos Seguintes
### <a name="batch-explorer-heat-map"></a>Mapa de calor de Explorador do batch
[Explorador do batch] [ batch_labs] é uma ferramenta de cliente autónoma gratuita e rica em funcionalidades, para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Explorador do batch contém um *mapa térmico* funcionalidade que fornece visualização da execução de tarefa. Quando está executando o [ParallelTasks] [ parallel_tasks_sample] aplicativo de exemplo, pode utilizar a funcionalidade de mapa térmico para visualizar facilmente a execução de tarefas paralelas em cada nó.


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

