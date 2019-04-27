---
title: Contagem de Estados de tarefas e nós – Azure Batch | Documentos da Microsoft
description: Conte o estado de tarefas do Azure Batch e nós para o ajudar a gerir e monitorizar as soluções do Batch de computação.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 574cdea61a474dda5d20254bfae9ff2f06044cca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775377"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorizar soluções do Batch por contagem de tarefas e nós pelo Estado

Para monitorizar e gerir soluções do Azure Batch em grande escala, terá de contagens precisas de recursos em vários Estados. O Azure Batch fornece operações eficientes para obter essas contagens de lote *tarefas* e *nós de computação*. Utilize estas operações em vez de consultas de lista potencialmente moroso que devolvem informações detalhadas sobre grandes coleções de tarefas ou nós.

* [Obter a conta de tarefa] [ rest_get_task_counts] obtém uma contagem agregada de tarefas ativas, em execução e concluídas numa tarefa e tarefas que foi concluída com êxito ou falha. 

  Por contagem de tarefas em cada Estado, mais facilmente pode exibir o progresso da tarefa para um usuário ou detetar atrasos inesperados ou falhas que possam afetar a tarefa. Contagens de tarefas de GET está disponível a partir da versão 2017-06-01.5.1 de API de serviço do Batch e de SDKs e ferramentas relacionados.

* [Listar a conta do Pool de nó] [ rest_get_node_counts] obtém o número de dedicado e de baixa prioridade nós de computação em cada pool que estão em vários Estados: criar, ocioso, offline, admitiu preempção for, reiniciar, recriar a imagem, iniciar e outros. 

  Por contagem de nós em cada Estado, pode determinar quando têm recursos de computação suficiente para executar os trabalhos e identificar potenciais problemas com seus conjuntos. Contagens de nó de conjunto de lista está disponível a partir da versão de 2018-03-01.6.1 de API de serviço do Batch e de SDKs e ferramentas relacionadas.

Se estiver a utilizar uma versão do serviço que não suporta operações de contagem de nó ou de contagem de tarefas, utilize uma consulta de lista para contar estes recursos. Também use uma consulta de lista para obter informações sobre outros recursos do Batch, como trabalhos, arquivos e aplicativos. Para obter mais informações sobre como aplicar filtros para consultas de lista, consulte [criar consultas para listar recursos do Batch com eficiência](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Contagens de estado de tarefa

A operação obter contagens de tarefas contagens de tarefas por seguintes Estados:

- **Active Directory** -uma tarefa que é colocado em fila e capaz de execução, mas não está atualmente atribuída ao nó de computação. Também é uma tarefa `active` caso se trate [dependente de uma tarefa principal](batch-task-dependencies.md) que ainda não foi concluído. 
- **Executar** -uma tarefa que foi atribuída a um nó de computação, mas ainda não foi concluído. Uma tarefa é contabilizada `running` quando o estado é `preparing` ou `running`, conforme indicado pela [obter informações sobre uma tarefa] [ rest_get_task] operação.
- **Concluído** -uma tarefa que já não é elegível para ser executado porque ele seja concluída com êxito, ou foi concluída com êxito tenta e também esgotado o limite de tentativas. 
- **Foi efetuada com êxito** -uma tarefa cujo resultado da execução de tarefa é `success`. Batch determina se uma tarefa teve êxito ou falha ao verificar a `TaskExecutionResult` propriedade o [executionInfo] [ rest_get_exec_info] propriedade.
- **Falha** uma tarefa cujo resultado da execução de tarefa é `failure`.

O exemplo de código .NET seguinte mostra como obter contagens de tarefas por Estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Pode utilizar um padrão semelhante para REST e outros idiomas com suporte para obter contagens de tarefas para uma tarefa. 

> [!NOTE]
> Versões de API de serviço do batch antes de 2018-08-01.7.0 também retornar um `validationStatus` propriedade na resposta obter contagens de tarefas. Esta propriedade indica se o Batch verificado que o estado de conta para manter a consistência com os Estados relatados na API de tarefas da lista. Um valor de `validated` indica apenas que o Batch verificada a consistência dos, pelo menos, uma vez para a tarefa. O valor da `validationStatus` propriedade não indica se as contagens que retorna obter contagens de tarefas estão atualmente atualizadas.
>

## <a name="node-state-counts"></a>Contagens de estado do nó

Nós pelos seguintes Estados em cada agrupamento de computação de contagens de operação contagens de nó de conjunto de lista. Contagens de agregação separadas são fornecidas para nós dedicados e nós de baixa prioridade de cada conjunto.

- **Criar** -VM alocada um Azure que ainda não foi iniciado para aderir a um conjunto.
- **Inatividade** -um nó de computação disponíveis que não está atualmente em execução uma tarefa.
- **LeavingPool** -um nó que está a sair do conjunto, uma vez que o utilizador removido explicitamente-lo ou porque o agrupamento é o redimensionamento ou o dimensionamento automático para baixo.
- **Offline** -um nó não é possível utilizar esse Batch para agendar tarefas novas.
- **Preempção** -um nó de baixa prioridade que foi removido do conjunto, porque o Azure recuperada a VM. A `preempted` nó pode ser reinicializado quando a capacidade VM de baixa prioridade de substituição disponível.
- **A reiniciar** -um nó que está a reiniciar.
- **A recriar imagem da** -um nó no qual está a ser reinstalado o sistema operativo.
- **Executar** -um nó que está a executar uma ou mais tarefas (que não seja a tarefa de início).
- **A iniciar** -um nó no qual está a iniciar o serviço Batch. 
- **StartTaskFailed** -um nó no qual o [tarefa de início] [ rest_start_task] falhou e esgotado todas as repetições e no qual `waitForSuccess` é definida na tarefa de início. O nó não pode ser utilizado para a execução de tarefas.
- **Desconhecido** - um nó que perdido o contacto com o serviço Batch e cujo estado não é conhecido.
- **Inutilizável** -um nó que não pode ser utilizado para a execução da tarefa devido a erros.
- **WaitingForStartTask** -um nó no qual a tarefa de início iniciou a execução, mas `waitForSuccess` é o conjunto e o início tarefa não foi concluída.

O seguinte trecho do c# mostra como listar as contagens de nó para todos os conjuntos na conta atual:

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
O seguinte trecho do c# mostra como listar as contagens de nó para um determinado conjunto na conta atual.

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
Pode utilizar um padrão semelhante para REST e outros idiomas com suporte para obter contagens de nó para conjuntos.
 
## <a name="next-steps"></a>Passos Seguintes

* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação, trabalhos e tarefas e fornece uma descrição geral das funcionalidades do serviço.

* Para obter informações sobre como aplicar filtros para consultas que listam os recursos do Batch, consulte [criar consultas para listar recursos do Batch com eficiência](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

