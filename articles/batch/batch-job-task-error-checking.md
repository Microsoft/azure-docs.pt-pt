---
title: Verificar erros de trabalho e tarefa-Azure batch | Microsoft Docs
description: Erros para verificar e solucionar problemas de trabalhos e tarefas
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 12/01/2019
ms.author: markscu
ms.openlocfilehash: c4e36d76bf85b9715a817dbeb7c690aa77f8d978
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852188"
---
# <a name="job-and-task-error-checking"></a>Verificação de erro de trabalho e tarefa

Há vários erros que podem ocorrer ao adicionar trabalhos e tarefas. A detecção de falhas para essas operações é simples porque todas as falhas são retornadas imediatamente pela API, CLI ou interface do usuário.  No entanto, há falhas que podem acontecer posteriormente quando trabalhos e tarefas são agendados e executados.

Este artigo aborda os erros que podem ocorrer depois que os trabalhos e as tarefas são enviados. Ele lista e explica os erros que precisam ser verificados e manipulados.

## <a name="jobs"></a>Tarefas

Um trabalho é um agrupamento de uma ou mais tarefas, na verdade, as tarefas que especificam as linhas de comando a serem executadas.

Ao adicionar um trabalho, os seguintes parâmetros podem ser especificados, o que pode influenciar como o trabalho pode falhar:

- [Restrições de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A propriedade `maxWallClockTime` pode, opcionalmente, ser especificada para definir a quantidade máxima de tempo que um trabalho pode estar ativo ou em execução. Se for excedido, o trabalho será encerrado com a propriedade `terminateReason` definida no [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de preparação do trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificado, uma tarefa de preparação de trabalho é executada na primeira vez em que uma tarefa é executada para um trabalho em um nó. A tarefa de preparação do trabalho pode falhar, o que fará com que a tarefa não seja executada e o trabalho não seja concluído.
- [Tarefa de liberação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de liberação de trabalho só poderá ser especificada se uma tarefa de preparação de trabalho estiver configurada. Quando um trabalho está sendo encerrado, a tarefa de liberação de trabalho é executada em cada um dos nós de pool em que uma tarefa de preparação de trabalho foi executada. Uma tarefa de liberação de trabalho pode falhar, mas o trabalho ainda será movido para um estado de `completed`.

### <a name="job-properties"></a>Propriedades do trabalho

As seguintes propriedades de trabalho devem ser verificadas em busca de erros:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - A propriedade `terminateReason` pode ter valores para indicar que o `maxWallClockTime`, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Ele também pode ser definido para indicar uma falha na tarefa se a propriedade de `onTaskFailure` de trabalho tiver sido definida adequadamente.
  - A propriedade [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) será definida se houver um erro de agendamento.
 
### <a name="job-preparation-tasks"></a>Tarefas de preparação do trabalho

Se uma tarefa de preparação de trabalho for especificada para um trabalho, uma instância dessa tarefa será executada na primeira vez em que uma tarefa para o trabalho for executada em um nó. A tarefa de preparação de trabalho configurada no trabalho pode ser considerada como um modelo de tarefa, com várias instâncias de tarefa de preparação de trabalho sendo executadas, até o número de nós em um pool.

As instâncias de tarefa de preparação do trabalho devem ser verificadas para determinar se Houveram erros:
- Quando uma tarefa de preparação de trabalho é executada, a tarefa que disparou a tarefa de preparação do trabalho será movida para um [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) de `preparing`; se a tarefa de preparação do trabalho falhar, a tarefa de disparo será revertida para o estado de `active` e não será executada.  
- Todas as instâncias da tarefa de preparação de trabalho que foram executadas podem ser obtidas do trabalho usando a [preparação da lista e a API de status da tarefa de liberação](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Assim como ocorre com qualquer tarefa, há [informações de execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode`e `result`.
- Se as tarefas de preparação do trabalho falharem, as tarefas de acionamento do trabalho não serão executadas, o trabalho não será concluído e ficará preso. O pool poderá ficar inutilizado se não houver nenhum outro trabalho com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de liberação do trabalho

Se uma tarefa de liberação de trabalho for especificada para um trabalho, quando um trabalho estiver sendo encerrado, uma instância da tarefa de liberação de trabalho será executada em cada um dos nós de pool em que uma tarefa de preparação de trabalho foi executada.  As instâncias de tarefa de liberação de trabalho devem ser verificadas para determinar se Houveram erros:
- Todas as instâncias da tarefa de liberação de trabalho que está sendo executada podem ser obtidas do trabalho usando a preparação da lista de APIs [e o status da tarefa de liberação](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Assim como ocorre com qualquer tarefa, há [informações de execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode`e `result`.
- Se uma ou mais tarefas de liberação de trabalho falharem, o trabalho ainda será encerrado e passará para um estado de `completed`.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por vários motivos:

- A linha de comando da tarefa falha, retornando com um código de saída diferente de zero.
- Há `resourceFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram baixados.
- Há `outputFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram carregados.
- O tempo decorrido da tarefa, especificado pela propriedade `maxWallClockTime` nas [restrições](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)de tarefa, foi excedido.

Em todos os casos, as seguintes propriedades devem ser verificadas quanto a erros e informações sobre os erros:
- A propriedade [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) de tarefas contém várias propriedades que fornecem informações sobre um erro. [resultado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa sempre será movida para o [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)de `completed`, independentemente de ter êxito ou falhado.

O impacto das falhas de tarefas no trabalho e de qualquer dependência de tarefa deve ser considerado.  A propriedade [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa a fim de configurar uma ação para dependências e para o trabalho.
- Para dependências, [dependencyaction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controla se as tarefas dependentes da tarefa com falha são bloqueadas ou são executadas.
- Para o trabalho, [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controla se a tarefa com falha leva ao trabalho que está sendo desabilitado, encerrado ou deixado inalterado.

## <a name="next-steps"></a>Passos seguintes

Verifique se seu aplicativo implementa a verificação de erros abrangente; pode ser essencial detectar e diagnosticar problemas imediatamente.
