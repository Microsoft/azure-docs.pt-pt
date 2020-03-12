---
title: Verifique se há erros de trabalho e tarefas - Lote Azure / Microsoft Docs
description: Erros para verificar e resolver problemas empregos e tarefas
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087010"
---
# <a name="job-and-task-error-checking"></a>Verificação de erros de trabalho e tarefa

Existem vários erros que podem ocorrer na adição de empregos e tarefas. A deteção de falhas nestas operações é simples porque quaisquer falhas são devolvidas imediatamente pela API, CLI ou UI.  No entanto, há falhas que podem acontecer mais tarde quando os postos de trabalho e as tarefas são programados e executados.

Este artigo cobre os erros que podem ocorrer após a apresentação de trabalhos e tarefas. Ele lista e explica os erros que precisam de ser verificados e tratados.

## <a name="jobs"></a>Tarefas

Um trabalho é um agrupamento de uma ou mais tarefas, as tarefas que realmente especificam as linhas de comando a executar.

Ao adicionar um trabalho, podem ser especificados os seguintes parâmetros que podem influenciar a forma como o trabalho pode falhar:

- [Restrições de Emprego](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A propriedade `maxWallClockTime` pode ser opcionalmente especificada para definir o máximo de tempo que um trabalho pode estar ativo ou em funcionamento. Se for ultrapassado, o trabalho será encerrado com o `terminateReason` imóvel definido na [execuçãoInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de preparação de emprego](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificado, uma tarefa de preparação de emprego é executada na primeira vez que uma tarefa é executada para um trabalho em um nó. A tarefa de preparação do trabalho pode falhar, o que levará a que a tarefa não seja executada e que o trabalho não seja concluído.
- [Tarefa de libertação de emprego](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de libertação de emprego só pode ser especificada se uma tarefa de preparação de trabalho estiver configurada. Quando um trabalho está sendo encerrado, a tarefa de libertação de emprego é executada em cada um dos nós da piscina onde uma tarefa de preparação de emprego foi executada. Uma tarefa de libertação de emprego pode falhar, mas o trabalho ainda vai passar para um estado `completed`.

### <a name="job-properties"></a>Propriedades de emprego

As seguintes propriedades de trabalho devem ser verificadas por erros:

- '[execuçãoInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - O `terminateReason` imóvel pode ter valores que indicam que o `maxWallClockTime`, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Também pode ser definido para indicar que uma tarefa falhou se o trabalho `onTaskFailure` propriedade foi definido adequadamente.
  - A propriedade [de agendamentoError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) é definida se tiver havido um erro de agendamento.
 
### <a name="job-preparation-tasks"></a>Tarefas de preparação de emprego

Se for especificada uma tarefa de preparação de emprego para um trabalho, então uma instância dessa tarefa será executada na primeira vez que uma tarefa para o trabalho é executada num nó. A tarefa de preparação de trabalho configurada no trabalho pode ser considerada como um modelo de tarefa, com múltiplas instâncias de tarefa de preparação de emprego sendo executadas, até o número de nós em uma piscina.

As instâncias de tarefa de preparação de emprego devem ser verificadas para determinar se houve erros:
- Quando uma tarefa de preparação de emprego for executada, então a tarefa que desencadeou a tarefa de preparação de emprego passará para um [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) de `preparing`; se a tarefa de preparação do trabalho falhar, a tarefa de desencadeamento reverterá para o estado `active` e não será executada.  
- Todas as instâncias da tarefa de preparação de emprego que foram executadas podem ser obtidas a partir do trabalho utilizando a API de [Preparação e Libertação](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) de Listas. Como em qualquer tarefa, existem informações de [execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com imóveis como `failureInfo`, `exitCode`e `result`.
- Se as tarefas de preparação de emprego falharem, as tarefas de desencadeamento não serão executadas, o trabalho não estará concluído e ficará preso. A piscina pode não ser utilizada se não houver outros trabalhos com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de libertação de emprego

Se for especificada uma tarefa de libertação de emprego para um trabalho, quando um trabalho está a ser encerrado, uma instância da tarefa de libertação de emprego é executada em cada um dos nós da piscina onde foi executada uma tarefa de preparação de emprego.  As instâncias de tarefa de libertação de emprego devem ser verificadas para determinar se houve erros:
- Todas as instâncias da tarefa de libertação de emprego em execução podem ser obtidas a partir do trabalho utilizando o Estado de [Preparação e Tarefa](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)de Libertação da Lista API . Como em qualquer tarefa, existem informações de [execução](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com imóveis como `failureInfo`, `exitCode`e `result`.
- Se uma ou mais tarefas de libertação de emprego falharem, o trabalho continuará a ser encerrado e a mudar-se para um estado `completed`.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por múltiplas razões:

- A linha de comando de tarefa falha, regressando com um código de saída não zero.
- Existem `resourceFiles` especificadas para uma tarefa, mas houve uma falha que significou que um ou mais ficheiros não descarregaram.
- Existem `outputFiles` especificados para uma tarefa, mas houve uma falha que significou que um ou mais ficheiros não foram carregados.
- O tempo decorrido para a tarefa, especificado pelo `maxWallClockTime` imóvel nos [condicionalismos](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)de tarefa, foi ultrapassado.

Em todos os casos, as seguintes propriedades devem ser verificadas para verificar se há erros e informações sobre os erros:
- As tarefas [execuçãoPropriedadeInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) contém múltiplas propriedades que fornecem informações sobre um erro. [resultado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa passará sempre para o [estado](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)`completed` , independentemente de ter sido bem sucedida ou falhada.

O impacto das falhas de tarefa sintetizam no trabalho e quaisquer dependências de tarefas.  A propriedade [ExitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa de configurar uma ação para dependências e para o trabalho.
- Para dependências, [a DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) controla se as tarefas dependentes da tarefa falhada estão bloqueadas ou são executadas.
- Para o trabalho, a [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) controla se a tarefa falhada leva a que o trabalho seja desativado, encerrado ou deixado inalterado.

### <a name="task-command-line-failures"></a>Falhas na linha de comando de tarefas

Quando a linha de comando de tarefa é executada, a saída é escrita para `stderr.txt` e `stdout.txt`. Além disso, a aplicação pode escrever para ficheiros de registo específicos da aplicação.

Se o nó da piscina em que uma tarefa foi executada ainda existir, então os ficheiros de registo podem ser obtidos e vistos. Por exemplo, o portal Azure lista e pode visualizar ficheiros de registo para uma tarefa ou um nó de piscina. Várias APIs também permitem que ficheiros de tarefas sejam listados e obtidos, tais como [Get From Task](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Devido a piscinas e nós de piscina frequentemente sendo efémeros, com os nós sendo continuamente adicionados e eliminados, então recomenda-se que os ficheiros de registo sejam persistidos. [Os ficheiros](https://docs.microsoft.com/azure/batch/batch-task-output-files) de saída de tarefas são uma forma conveniente de guardar ficheiros de registo para o Armazenamento Azure.

### <a name="output-file-failures"></a>Falhas nos ficheiros de saída
Em cada upload de ficheiros, o Batch escreve dois ficheiros de registo para o nó de cálculo, `fileuploadout.txt` e `fileuploaderr.txt`. Pode examinar estes ficheiros de registo para saber mais sobre uma falha específica. Nos casos em que o upload do ficheiro nunca foi tentado, por exemplo porque a tarefa em si não podia ser executada, então estes ficheiros de registo não existirão.  

## <a name="next-steps"></a>Passos seguintes

Verifique se a sua aplicação implementa uma verificação abrangente de erros; pode ser fundamental detetar e diagnosticar rapidamente problemas.
