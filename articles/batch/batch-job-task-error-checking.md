---
title: Verificar a existência de erros nos trabalhos e tarefas
description: Saiba mais sobre erros a verificar e como resolver problemas de trabalhos e tarefas.
author: mscurrell
ms.topic: how-to
ms.date: 11/23/2020
ms.author: markscu
ms.openlocfilehash: d8cf3b5e28d4455e00e0bdcbae2063771d3e8acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95736804"
---
# <a name="job-and-task-error-checking"></a>Verificação de erros de trabalho e de tarefa

Existem vários erros que podem ocorrer ao adicionar empregos e tarefas. A deteção de falhas para estas operações é simples porque quaisquer falhas são devolvidas imediatamente pela API, CLI ou UI. No entanto, também há falhas que podem acontecer mais tarde, quando os postos de trabalho e as tarefas são agendados e executados.

Este artigo cobre os erros que podem ocorrer após a apresentação de trabalhos e tarefas e como verificar e lidar com eles.

## <a name="jobs"></a>Tarefas

Um trabalho é um agrupamento de uma ou mais tarefas, com as tarefas realmente especificando as linhas de comando a serem executadas.

Ao adicionar um trabalho, podem ser especificados os seguintes parâmetros que podem influenciar a forma como o trabalho pode falhar:

- [Restrições de emprego](/rest/api/batchservice/job/add#jobconstraints)
  - A `maxWallClockTime` propriedade pode opcionalmente ser especificada para definir o tempo máximo que um trabalho pode estar ativo ou em execução. Se for ultrapassado, o trabalho será encerrado com o `terminateReason` imóvel definido na [execuçãoInfo](/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de preparação de emprego](/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificado, uma tarefa de preparação de emprego é executada na primeira vez que uma tarefa é executada para um trabalho num nó. A tarefa de preparação do trabalho pode falhar, o que levará a que a tarefa não seja executada e que o trabalho não seja concluído.
- [Tarefa de lançamento de emprego](/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de libertação de emprego só pode ser especificada se uma tarefa de preparação de emprego for configurada. Quando um trabalho está a ser encerrado, a tarefa de libertação de emprego é executada em cada um dos nós de piscina onde foi executada uma tarefa de preparação de emprego. Uma tarefa de libertação de emprego pode falhar, mas o trabalho ainda vai mudar para um `completed` estado.

### <a name="job-properties"></a>Propriedades de emprego

As seguintes propriedades de trabalho devem ser verificadas para verificar se há erros:

- '[execuçãoInfo](/rest/api/batchservice/job/get#jobexecutioninformation)':
  - O `terminateReason` imóvel pode ter valores que indiquem que `maxWallClockTime` o, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Também pode ser configurado para indicar que uma tarefa falhou se a propriedade do trabalho `onTaskFailure` foi definida adequadamente.
  - A [propriedade de agendamentoError](/rest/api/batchservice/job/get#jobschedulingerror) é definida se tiver havido um erro de agendamento.

### <a name="job-preparation-tasks"></a>Tarefas de preparação de emprego

Se uma tarefa de preparação de emprego for especificada para um trabalho, então uma instância dessa tarefa será executada pela primeira vez uma tarefa para o trabalho é executada num nó. A tarefa de preparação do trabalho configurada no trabalho pode ser considerada como um modelo de tarefa, com múltiplas instâncias de preparação de trabalho sendo executadas, até o número de nós em uma piscina.

As instâncias de preparação do trabalho devem ser verificadas para determinar se houve erros:

- Quando uma tarefa de preparação de emprego é executada, então a tarefa que desencadeou a tarefa de preparação do trabalho passará para um [estado](/rest/api/batchservice/task/get#taskstate) de ; se a tarefa de `preparing` preparação do trabalho falhar, a tarefa de desencadeamento reverterá para o `active` Estado e não será executada.
- Todos os casos da tarefa de preparação de trabalho que foram executados podem ser obtidos a partir do trabalho usando a API de [Preparação e Libertação](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) de Listas. Como em qualquer tarefa, há [informação](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) de execução disponível com propriedades `failureInfo` `exitCode` como, e `result` .
- Se as tarefas de preparação de emprego falharem, as tarefas de desencadeamento não serão executadas, o trabalho não será concluído e ficará preso. A piscina pode ficar sem ser utetilizada se não houver outros trabalhos com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de libertação de emprego

Se uma tarefa de libertação de emprego for especificada para um trabalho, então quando um trabalho está sendo encerrado, uma instância da tarefa de libertação de emprego é executada em cada nó de piscina onde uma tarefa de preparação de emprego foi executada. As instâncias de tarefa de libertação de emprego devem ser verificadas para determinar se houve erros:

- Todos os casos da tarefa de libertação de emprego que está a ser executada podem ser obtidos a partir do trabalho utilizando o Estado de [Preparação e Libertação da Lista](/rest/api/batchservice/job/listpreparationandreleasetaskstatus)API . Como em qualquer tarefa, há [informação](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) de execução disponível com propriedades `failureInfo` `exitCode` como, e `result` .
- Se uma ou mais tarefas de libertação de emprego falharem, então o trabalho continuará a ser encerrado e passará para um `completed` estado.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por múltiplas razões:

- A linha de comando de tarefa falha, retornando com um código de saída não zero.
- Há `resourceFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais ficheiros não descarregavam.
- Há `outputFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais ficheiros não eram carregados.
- O tempo decorrido para a tarefa, especificado pelo `maxWallClockTime` imóvel nas [restrições](/rest/api/batchservice/task/add#taskconstraints)de tarefa, foi ultrapassado.

Em todos os casos, as seguintes propriedades devem ser verificadas quanto a erros e informações sobre os erros:

- A execução de [tarefasInfo](/rest/api/batchservice/task/get#taskexecutioninformation) propriedade contém várias propriedades que fornecem informações sobre um erro. [o resultado](/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa passará sempre para o `completed` [Estado,](/rest/api/batchservice/task/get#taskstate)independentemente de ter sido bem sucedida ou fracassada.

O impacto das falhas de tarefa no trabalho e de quaisquer dependências de tarefas deve ser considerado. A [propriedade de saídaConditions](/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa de configurar uma ação para dependências e para o trabalho.

- Para as dependências, [a DependencyAction](/rest/api/batchservice/task/add#dependencyaction) controla se as tarefas dependentes da tarefa falhada estão bloqueadas ou executadas.
- Para o trabalho, o [JobAction](/rest/api/batchservice/task/add#jobaction) controla se a tarefa falhada leva a que o trabalho seja desativado, encerrado ou deixado inalterado.

### <a name="task-command-line-failures"></a>Falhas da linha de comando de tarefa

Quando a linha de comando de tarefa é executada, a saída é escrita para `stderr.txt` e `stdout.txt` . . Além disso, a aplicação pode escrever para ficheiros de registo específicos da aplicação.

Se o nó de piscina em que uma tarefa foi executada ainda existir, então os ficheiros de registo podem ser obtidos e visualizados. Por exemplo, o portal Azure lista e pode visualizar ficheiros de registo para uma tarefa ou um nó de piscina. As APIs múltiplas também permitem que os ficheiros de tarefas sejam listados e obtidos, tais como [Get From Task](/rest/api/batchservice/file/getfromtask).

Uma vez que as piscinas e os nós de piscina são frequentemente efémeros, com os nós a serem continuamente adicionados e eliminados, recomendamos a poupança de ficheiros de registo. [Os ficheiros de saída de tarefas](./batch-task-output-files.md) são uma forma conveniente de guardar ficheiros de registo para o Azure Storage.

As linhas de comando executadas por tarefas nos nós de computação não funcionam sob uma concha, pelo que não podem tirar partido de características da concha, como a expansão variável do ambiente. Para tirar partido de tais características, deve [invocar a concha na linha de comando.](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables)

### <a name="output-file-failures"></a>Falhas nos ficheiros de saída

Em cada upload de ficheiros, o Batch escreve dois ficheiros de registo para o nó de computação `fileuploadout.txt` e `fileuploaderr.txt` . Pode examinar estes ficheiros de registo para saber mais sobre uma falha específica. Nos casos em que o upload do ficheiro nunca foi tentado, por exemplo porque a tarefa em si não podia ser executada, então estes ficheiros de registo não existirão.  

## <a name="next-steps"></a>Passos seguintes

- Verifique se a sua aplicação implementa uma verificação completa de erros; pode ser fundamental para detetar e diagnosticar rapidamente problemas.
- Saiba mais sobre [empregos e tarefas.](jobs-and-tasks.md)