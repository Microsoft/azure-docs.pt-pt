---
title: Conde estados para tarefas e nódosos
description: Conte o estado das tarefas do Lote Azure e calcule os nódosos para ajudar a gerir e monitorizar as soluções do Lote.
ms.date: 09/07/2018
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: e063ef56deeb7fa0f2a217f48b1c23a810a9d890
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726694"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorize as soluções do Lote contando tarefas e nódosos por estado

Para monitorizar e gerir soluções de lote de grande escala do Lote, é necessário contabilizar recursos precisos em vários estados. O Azure Batch fornece operações eficientes para obter estas contagens para *tarefas* de lote e *nódeos de cálculo*. Utilize estas operações em vez de consultas de lista potencialmente morosas que devolvam informações detalhadas sobre grandes coleções de tarefas ou nó.

* [Obter Task Counts][rest_get_task_counts] obtém uma contagem agregada de tarefas ativas, em execução e concluídas num trabalho, e de tarefas que foram bem sucedidas ou falhadas. 

  Ao contar tarefas em cada estado, pode mais facilmente apresentar o progresso do trabalho a um utilizador, ou detetar atrasos ou falhas inesperados que possam afetar o trabalho. O Get Task Counts está disponível a partir da versão API do Serviço de Lote 2017-06-01.5.1 e sDKs e ferramentas relacionadas.

* [List Pool Node Counts][rest_get_node_counts] obtém o número de nós de computação dedicados e de baixa prioridade em cada piscina que estão em vários estados: criação, ocioso, offline, pré-empreitado, reinicialização, reimaging, starting, e outros. 

  Ao contar nós em cada estado, pode determinar quando tem recursos computacionais adequados para gerir os seus trabalhos e identificar potenciais problemas com as suas piscinas. List Pool Node Counts está disponível a partir da versão API do Serviço de Lote 2018-03-01.6.1 e SDKs e ferramentas relacionadas.

Se estiver a usar uma versão do serviço que não suporta as operações de contagem de tarefas ou de contagem de nós, utilize uma consulta de lista para contar estes recursos. Use também uma consulta de lista para obter informações sobre outros recursos do Lote, tais como aplicações, ficheiros e empregos. Para obter mais informações sobre a aplicação de filtros para listar consultas, consulte [Criar consultas para listar os recursos](batch-efficient-list-queries.md)do Lote de forma eficiente .

## <a name="task-state-counts"></a>Contagem de estado-tarefa

A operação Get Task Counts conta tarefas pelos seguintes estados:

- **Ativo** - Uma tarefa que está na fila e é capaz de executar, mas que não está atualmente atribuída a um nó de cálculo. Uma tarefa é também `active` se estiver dependente de uma [tarefa-mãe](batch-task-dependencies.md) que ainda não tenha sido concluída. 
- **Execução** - Uma tarefa que foi atribuída a um nó de cálculo, mas ainda não concluída. Uma tarefa é contada como quando o `running` seu estado é ou , como indicado pelo Obter `preparing` `running` informações sobre uma operação de [tarefa.][rest_get_task]
- **Concluída** - Uma tarefa que já não é elegível para executar, porque terminou com sucesso, ou terminou sem sucesso e também esgotou o seu limite de retry. 
- **Succeeded** - Uma tarefa cujo resultado da execução da tarefa é `success` . O lote determina se uma tarefa foi bem sucedida ou falhou verificando a `TaskExecutionResult` propriedade da propriedade [executionInfo.][rest_get_exec_info]
- **Falhou** Uma tarefa cujo resultado da execução da tarefa `failure` é.

A seguinte amostra de código .NET mostra como recuperar as contagens de tarefa por estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Você pode usar um padrão semelhante para REST e outras línguas apoiadas para obter contagens de tarefa para um trabalho. 

> [!NOTE]
> As versões API do Serviço de Lote antes de 2018-08-01.7.0 também devolvem um `validationStatus` imóvel na resposta Get Task Counts. Esta propriedade indica se o Batch verificou que o estado conta para a consistência com os Estados relatados na API de Tarefas de Lista. Um valor indica `validated` apenas que o Batch verificou a consistência pelo menos uma vez para o trabalho. O valor da `validationStatus` propriedade não indica se as contagens que obtêm devoluções de Contagens de Tarefa estão atualmente atualizadas.
>

## <a name="node-state-counts"></a>Contagem de estado do nó

A operação List Pool Node Counts conta com nós de cálculo pelos seguintes estados em cada piscina. São previstas contagens agregadas separadas para nós dedicados e nós de baixa prioridade em cada piscina.

- **Criação** - Um VM alocado a Azure que ainda não começou a juntar-se a uma piscina.
- **Idle** - Um nó de cálculo disponível que não está atualmente a executar uma tarefa.
- **LeavingPool** - Um nó que está a sair da piscina, seja porque o utilizador a removeu explicitamente ou porque a piscina está a redimensionar ou a autodimensionar-se.
- **Offline** - Um nó que o Batch não pode usar para agendar novas tarefas.
- **Preempted** - Um nó de baixa prioridade que foi removido da piscina porque Azure recuperou o VM. Um `preempted` nó pode ser reinicializado quando a substituição da capacidade vm de baixa prioridade estiver disponível.
- **Reiniciar** - Um nó que está a reiniciar.
- **Reimaging** - Um nó no qual o sistema operativo está a ser reinstalado.
- **Execução** - Um nó que está a executar uma ou mais tarefas (para além da tarefa inicial).
- **Início** - Um nó no qual o serviço De lote está a começar. 
- **StartTaskFailed** - Um nó no qual a [tarefa][rest_start_task] inicial falhou e esgotou todas as tentativas, e no qual `waitForSuccess` está definido na tarefa inicial. O nó não é utilizável para executar tarefas.
- **Desconhecido** - Um nó que perdeu o contacto com o serviço Batch e cujo estado não é conhecido.
- **Inutilizável** - Um nó que não pode ser usado para execução de tarefas por causa de erros.
- **WaitingForStartTask** - Um nó no qual a tarefa inicial começou a funcionar, mas está definido e a tarefa inicial `waitForSuccess` ainda não está concluída.

O seguinte c# snippet mostra como listar contagens de nó para todos os pools na conta corrente:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
O seguinte c# snippet mostra como listar contagens de nó para um dado pool na conta corrente.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Você pode usar um padrão semelhante para REST e outras línguas apoiadas para obter contagens de nó para piscinas.
 
## <a name="next-steps"></a>Próximos passos

* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo discute os recursos primários do Lote, tais como piscinas, nós de computação, empregos e tarefas, e fornece uma visão geral das características do serviço.

* Para obter informações sobre a aplicação de filtros a consultas que listam os recursos do Lote, consulte [Criar consultas para listar os recursos](batch-efficient-list-queries.md)do Lote de forma eficiente .


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

