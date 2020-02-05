---
title: Contar Estados para tarefas e nós-Azure batch | Microsoft Docs
description: Conte o estado das tarefas do lote do Azure e nós de computação para ajudar a gerenciar e monitorar soluções de lote.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023927"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorar soluções de lote por meio da contagem de tarefas e nós por Estado

Para monitorar e gerenciar soluções de lote do Azure em grande escala, você precisa de contagens precisas de recursos em vários Estados. O lote do Azure fornece operações eficientes para obter essas contagens para *tarefas* em lotes e *nós de computação*. Use essas operações em vez de consultas de lista potencialmente demoradas que retornam informações detalhadas sobre grandes coleções de tarefas ou nós.

* [Obter contagens de tarefas][rest_get_task_counts] Obtém uma contagem agregada de tarefas ativas, em execução e concluídas em um trabalho e de tarefas que tiveram êxito ou falharam. 

  Ao contar tarefas em cada Estado, você pode exibir mais facilmente o progresso do trabalho para um usuário ou detectar atrasos inesperados ou falhas que podem afetar o trabalho. Obter contagens de tarefas está disponível a partir da API do serviço de lote versão 2017-06-01.5.1 e SDKs e ferramentas relacionadas.

* [Listar contagens de nó do pool][rest_get_node_counts] Obtém o número de nós de computação dedicados e de baixa prioridade em cada pool que estão em vários Estados: criação, ociosidade, offline, preempção, reinicialização, recriação de imagens, início e outros. 

  Ao contar nós em cada Estado, você pode determinar quando você tem recursos de computação adequados para executar seus trabalhos e identificar possíveis problemas com seus pools. Listar contagens de nó de pool está disponível a partir da versão da API do serviço de lote 2018 -03-01.6.1 e ferramentas e SDKs relacionados.

Se você estiver usando uma versão do serviço que não dá suporte à contagem de tarefas ou às operações de contagem de nós, use uma consulta de lista em vez de contar esses recursos. Use também uma consulta de lista para obter informações sobre outros recursos do lote, como aplicativos, arquivos e trabalhos. Para obter mais informações sobre como aplicar filtros a consultas de lista, consulte [criar consultas para listar recursos do lote com eficiência](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Contagens de estado da tarefa

A operação obter contagens de tarefas conta as tarefas dos seguintes Estados:

- **Ativo** -uma tarefa que é enfileirada e pode ser executada, mas não está atribuída no momento a um nó de computação. Uma tarefa também será `active` se ela for [dependente de uma tarefa pai](batch-task-dependencies.md) que ainda não foi concluída. 
- **Executando** -uma tarefa que foi atribuída a um nó de computação, mas ainda não foi concluída. Uma tarefa é contada como `running` quando seu estado é `preparing` ou `running`, conforme indicado pela operação [obter informações sobre uma tarefa][rest_get_task] .
- **Concluído** -uma tarefa que não está mais qualificada para ser executada, pois ela foi concluída com êxito ou não foi concluída com êxito e também esgotou seu limite de tentativas. 
- **Êxito** -uma tarefa cujo resultado da execução da tarefa é `success`. O lote determina se uma tarefa teve êxito ou falhou verificando a propriedade `TaskExecutionResult` da propriedade [executionInfo][rest_get_exec_info] .
- **Com falha** Uma tarefa cujo resultado da execução da tarefa é `failure`.

O exemplo de código .NET a seguir mostra como recuperar contagens de tarefas por Estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Você pode usar um padrão semelhante para REST e outros idiomas com suporte para obter contagens de tarefas para um trabalho. 

> [!NOTE]
> Versões da API do serviço de lote antes de 2018 -08-01.7.0 também retornam uma propriedade `validationStatus` na resposta obter contagens de tarefas. Essa propriedade indica se o lote verificou se o estado conta para consistência com os Estados relatados na API listar tarefas. Um valor de `validated` indica apenas que o lote verificou a consistência pelo menos uma vez para o trabalho. O valor da propriedade `validationStatus` não indica se as contagens que obtêm contagens de tarefas retornam estão atualizadas no momento.
>

## <a name="node-state-counts"></a>Contagens de estado do nó

A operação contagens de nó do pool de lista conta os nós de computação pelos seguintes Estados em cada pool. Contagens de agregação separadas são fornecidas para nós dedicados e nós de baixa prioridade em cada pool.

- **Criando** -uma VM alocada pelo Azure que ainda não foi iniciada para ingressar em um pool.
- **Idle** -um nó de computação disponível que não está executando uma tarefa no momento.
- **LeavingPool** -um nó que está saindo do pool, porque o usuário o removeu explicitamente ou porque o pool está sendo redimensionado ou o dimensionamento automático.
- **Offline** -um nó que o lote não pode usar para agendar novas tarefas.
- **Preempção** -um nó de baixa prioridade que foi removido do pool porque o Azure recuperou a VM. Um nó `preempted` pode ser reinicializado quando a capacidade da VM de baixa prioridade de substituição está disponível.
- **Reinicializando** -um nó que está sendo reiniciado.
- Refazendo a **imagem** -um nó no qual o sistema operacional está sendo reinstalado.
- **Em execução** -um nó que está executando uma ou mais tarefas (além da tarefa inicial).
- **Iniciando** -um nó no qual o serviço de lote está sendo iniciado. 
- **StartTaskFailed** -um nó no qual a [tarefa inicial][rest_start_task] falhou e se esgotou todas as repetições e na qual `waitForSuccess` é definida na tarefa inicial. O nó não pode ser usado para executar tarefas.
- **Desconhecido** -um nó que perdeu contato com o serviço de lote e cujo estado não é conhecido.
- **Inutilizável** -um nó que não pode ser usado para a execução da tarefa devido a erros.
- **WaitingForStartTask** -um nó no qual a tarefa inicial começou a ser executada, mas `waitForSuccess` está definida e a tarefa inicial não foi concluída.

O trecho C# a seguir mostra como listar contagens de nós para todos os pools na conta atual:

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
O trecho C# a seguir mostra como listar contagens de nós para um determinado pool na conta atual.

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
Você pode usar um padrão semelhante para REST e outros idiomas com suporte para obter contagens de nós para pools.
 
## <a name="next-steps"></a>Passos seguintes

* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo discute os principais recursos do lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço.

* Para obter informações sobre como aplicar filtros a consultas que listam recursos do lote, consulte [criar consultas para listar recursos do lote com eficiência](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

