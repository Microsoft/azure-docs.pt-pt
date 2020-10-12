---
title: Conde estados para tarefas e nódoas
description: Conte o estado das tarefas do Lote Azure e os nós de computação para ajudar a gerir e monitorizar as soluções do Batch.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960593"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorize soluções de Lote contando tarefas e nódes por estado

Para monitorizar e gerir soluções Azure Batch em larga escala, poderá ser necessário determinar a contagem de recursos em vários estados. O Azure Batch fornece operações eficientes para obter contagens para tarefas de Batch e nós de computação. Você pode usar estas operações em vez de consultas de lista potencialmente demoradas que devolvem informações detalhadas sobre grandes coleções de tarefas ou nós.

- [Obter Task Counts](/rest/api/batchservice/job/gettaskcounts) obtém uma contagem agregada de tarefas ativas, executantes e concluídas num trabalho, e de tarefas que foram bem sucedidas ou falhadas. 

  Ao contar tarefas em cada estado, pode apresentar mais facilmente o progresso do trabalho a um utilizador, ou detetar atrasos ou falhas inesperadas que possam afetar o trabalho. Get Task Counts está disponível a partir da versão API do Serviço de Lote 2017-06-01.5.1 e SDKs e ferramentas relacionadas.

- [List Pool Node Counts](/rest/api/batchservice/account/listpoolnodecounts) obtém o número de nós computacional dedicados e de baixa prioridade em cada piscina que estão em vários estados: criar, ficar inativo, offline, preempted, reiniciar, reimaging, iniciar, e outros.

  Ao contar nós em cada estado, pode determinar quando tem recursos computativos adequados para executar os seus trabalhos e identificar potenciais problemas com as suas piscinas. List Pool Node Counts está disponível a partir da versão API do Serviço de Lote 2018-03-01.6.1 e SDKs e ferramentas relacionadas.

Note que, por vezes, os números devolvidos por estas operações podem não estar atualizados. Se precisar de ter a certeza de que uma contagem é precisa, use uma consulta de lista para contar estes recursos. As consultas de lista também permitem obter informações sobre outros recursos do Batch, tais como aplicações. Para obter mais informações sobre a aplicação de filtros para listar consultas, consulte [criar consultas para listar os recursos do Lote de forma eficiente.](batch-efficient-list-queries.md)

## <a name="task-state-counts"></a>Estado de tarefa conta

A operação Get Task Counts conta as tarefas pelos seguintes estados:

- **Ativo** - Uma tarefa que é em fila e capaz de executar, mas que não está atualmente atribuída a um nó de computação. Uma tarefa também é `active` se depender de uma tarefa dos [pais](batch-task-dependencies.md) que ainda não tenha sido concluída. 
- **Execução** - Uma tarefa que foi atribuída a um nó de computação, mas que ainda não foi concluída. Uma tarefa é contada como `running` quando o seu estado é ou , como indicado pela `preparing` `running` Informação obter sobre uma operação [de tarefa.](/rest/api/batchservice/task/get)
- **Concluída** - Uma tarefa que já não é elegível para executar, porque ou terminou com sucesso, ou terminou sem sucesso e também esgotou o seu limite de recandidologia. 
- **Bem sucedido** - Uma tarefa cujo resultado da execução de tarefas é `success` . O lote determina se uma tarefa foi bem sucedida ou falhou verificando a `TaskExecutionResult` propriedade da [execuçãoInfo.](/rest/api/batchservice/task/get)
- **Falhou** Uma tarefa cujo resultado da execução de tarefas é `failure` .

A seguinte amostra de código .NET mostra como recuperar contagens de tarefas por estado:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Você pode usar um padrão semelhante para REST e outras línguas apoiadas para obter contagens de tarefas para um trabalho. 

> [!NOTE]
> As versões API do Serviço de Lote antes de 2018-08-01.7.0 também devolvem uma `validationStatus` propriedade na resposta Get Task Counts. Esta propriedade indica se Batch verificou se o estado conta para consistência com os estados relatados na Lista tasks API. Um valor `validated` indica apenas que o Batch verificou a consistência pelo menos uma vez para o trabalho. O valor do `validationStatus` imóvel não indica se as contagens que obtém as devoluções de Task Counts estão atualmente atualizadas.

## <a name="node-state-counts"></a>Contagem do estado do nó

A operação List Pool Node Counts conta os nós computacional pelos seguintes estados em cada piscina. São fornecidas contagens agregadas separadas para nós dedicados e nós de baixa prioridade em cada piscina.

- **Criação** - Um VM alocado a azure que ainda não começou a juntar-se a uma piscina.
- **Idle** - Um nó de computação disponível que não está atualmente a executar uma tarefa.
- **LeavingPool** - Um nó que está a sair da piscina, seja porque o utilizador o removeu explicitamente ou porque a piscina está a redimensionar ou a autoscalar para baixo.
- **Offline** - Um nó que o Batch não pode usar para agendar novas tarefas.
- **Preempted** - Um nó de baixa prioridade que foi removido da piscina porque Azure recuperou o VM. Um `preempted` nó pode ser reinicializado quando a substituição da capacidade de VM de baixa prioridade está disponível.
- **Reinicialização** - Um nó que está a reiniciar.
- **Reimaging** - Um nó no qual o sistema operativo está a ser reinstalado.
- **Execução** - Um nó que executa uma ou mais tarefas (para além da tarefa inicial).
- **Início** - Um nó no qual o serviço Batch está a começar. 
- **StartTaskFailed** - Um nó no qual a [tarefa inicial](/rest/api/batchservice/pool/add#starttask) falhou e esgotou todas as retrações, e sobre a qual está definida a `waitForSuccess` tarefa inicial. O nó não é utilizável para executar tarefas.
- **Desconhecido** - Um nó que perdeu o contacto com o serviço Batch e cujo estado não é conhecido.
- **Inutilizável** - Um nó que não pode ser usado para execução de tarefas por causa de erros.
- **WaitingForStartTask** - Um nó no qual a tarefa inicial começou a ser iniciada, mas `waitForSuccess` está definida e a tarefa inicial ainda não está concluída.

O seguinte corte C# mostra como listar as contagens de nó para todas as piscinas na conta corrente:

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

O seguinte corte C# mostra como listar as contagens de nó para uma dada piscina na conta corrente.

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

Você pode usar um padrão semelhante para REST e outras línguas suportadas para obter contagem de nó para piscinas.

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba como aplicar filtros a consultas que listam recursos do Lote, consulte [Criar consultas para listar os recursos do Batch de forma eficiente.](batch-efficient-list-queries.md)
