---
title: Manipulação e deteção de erros em Lote Azure
description: Saiba mais sobre o manuseamento de erros nos fluxos de trabalho do serviço Batch do ponto de vista do desenvolvimento.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 07b9d43ea9bdf21fe3188c4481e6dd0c86374607
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791093"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Manipulação e deteção de erros em Lote Azure

Por vezes, pode achar necessário lidar com falhas de tarefa e aplicação dentro da sua solução De Lote. Este artigo fala sobre tipos de erros e como resolvê-los.

## <a name="error-codes"></a>Códigos de erro

Os tipos gerais de erros incluem:

- Falhas de networking para pedidos que nunca chegaram ao Lote, ou quando a resposta do Lote não chegou ao cliente a tempo.
- Erros internos do servidor (resposta http do código de estado padrão 5xx).
- Erros relacionados com estrangulamento, tais como 429 ou 503 respostas do código de estado HTTP com o cabeçalho Retry-after.
- Erros 4xx como Já Existe e InvalidOperation. Isto significa que o recurso não está no estado correto para a transição do Estado.

Para obter informações detalhadas sobre códigos de erro específicos, incluindo códigos de erro para rest API, serviço de lote e tarefa/agendamento de trabalho, consulte [o Estado do Lote e códigos](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)de erro .

## <a name="application-failures"></a>Falhas de aplicações

Durante a execução, uma aplicação poderá produzir resultados de diagnóstico que pode utilizar para resolver problemas. Tal como descrito em [Ficheiros e Diretórios,](files-and-directories.md)o serviço Batch escreve a saída padrão e a saída padrão de erro `stdout.txt` para e `stderr.txt` ficheiros no diretório de tarefas no nó computacional.

Pode utilizar o portal do Azure ou um dos SDKs do Batch para transferir estes ficheiros. Por exemplo, pode utilizar [ComputeNode.GetNodeFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) e [CloudTask.GetNodeFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask) na biblioteca .NET do Batch, para obter estes e outros ficheiros para fins de resolução de problemas.

## <a name="task-errors"></a>Erros de tarefa

Os erros de tarefa saem em várias categorias.

### <a name="pre-processing-errors"></a>Erros de pré-processamento

Se ocorrer uma falha ao iniciar uma tarefa, é definido um erro de pré-processamento para a mesma.  

Erros de pré-processamento podem ocorrer se os ficheiros de recursos da tarefa se moverem, a conta de armazenamento já não está disponível, ou se encontrou outro problema que impediu a cópia bem sucedida dos ficheiros para o nó.

### <a name="file-upload-errors"></a>Erros de upload de ficheiros

Se os ficheiros especificados para uma tarefa não conseguirem fazer o upload por qualquer motivo, é definido um erro de upload de ficheiros para a tarefa.

Os erros de carregamento de ficheiros podem ocorrer se a SAS fornecida para aceder ao Armazenamento do Azure for inválida ou não fornecer permissões de escrita, se a conta de utilizador já não estiver disponíveis ou se tiver ocorrido outro problema que impediu a cópia bem-sucedida dos ficheiros para o nós.

### <a name="application-errors"></a>Erros de aplicações

O processo especificado pela linha de comandos da tarefa também pode falhar. Considera-se que o processo falhou quando o processo que a tarefa executa devolve um código de saída diferente de zero (veja *Códigos de saída de tarefas* na secção seguinte).

Para erros de aplicação, pode configurar o Batch para tentar automaticamente a tarefa até um número especificado de vezes.

### <a name="constraint-errors"></a>Erros de restrição

Pode definir uma restrição que especifica a duração de execução máxima de um trabalho, a *maxWallClockTime*. Isto pode ser útil para terminar tarefas que não progridem.

Quando o tempo máximo tiver sido excedido, a tarefa é marcada como *concluída*, mas o código de saída é definido como `0xC000013A` e o campo *schedulingError* é marcado como `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Códigos de saída de tarefa

Conforme mencionado anteriormente, o serviço Batch marca uma tarefa como falhada se o processo executado por essa tarefa devolver um código de saída diferente de zero. Quando uma tarefa executa um processo, o Batch preenche a propriedade do código de saída dessa tarefa com o código de devolução do processo.

É importante que tenha em atenção que o código de saída de uma tarefa não é determinado pelo serviço do Batch. É determinado um código de saída de tarefas pelo próprio processo ou o sistema operativo no qual o processo foi executado.

## <a name="task-failures-or-interruptions"></a>Falhas de tarefas ou interrupções

Por vezes, as tarefas podem falhar ou ser interrompidas. A aplicação de tarefa em si pode falhar, o nó em que a tarefa está em execução pode ser reiniciado, ou o nó pode ser removido da piscina durante uma operação de redimensionação (se a política de deatribuição do pool estiver definida para remover os nós imediatamente sem esperar que as tarefas terminem). Em todos os casos, o Batch pode recolocar automaticamente a tarefa na fila para execução noutro nó.

Também é possível que uma questão intermitente faça com que uma tarefa deixe de responder ou deleve muito tempo a ser executada. Pode definir o intervalo de execução máxima de uma tarefa. Se o intervalo de execução máxima for excedido, o serviço de Batch interrompe a aplicação de tarefas.

## <a name="connect-to-compute-nodes"></a>Ligar a comparação de nódosos

Pode iniciar sessão remotamente num nó de computação para realizar depurações e resolução de problemas adicionais. Pode utilizar o portal do Azure para transferir um ficheiro do protocolo RDP (Remote Desktop Protocol) para nós do Windows e obter informações de ligação Secure Shell (SSH) para nós do Linux. Também pode fazê-lo utilizando as APIs do lote, tais como com [o Lote .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) ou [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Para ligar a um nó através de DRP ou SSH, tem de criar, primeiro, um utilizador no nó. Para tal, pode utilizar o portal do Azure, [adicionar uma conta de utilizador a um nó](https://docs.microsoft.com/rest/api/batchservice/computenode/adduser) com a API REST do Batch, chamar o método [ComputeNode.CreateComputeNodeUser](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode) no .NET do Batch ou chamar o método [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) no módulo Python do Batch.

Se precisar de restringir ou desativar o acesso RDP ou SSH para computação de nós, consulte [Configurar ou desativar acesso remoto para computação de nós num conjunto do Azure Batch](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Nódosos problemáticos

Em situações onde algumas das suas tarefas estejam a falhar, a aplicação cliente ou o serviço Batch podem examinar os metadados das tarefas com falhas para identificar um nó a funcionar incorretamente. Cada nó num conjunto recebe um ID exclusivo e o nó no qual é executada uma tarefa está incluído nos metadados da tarefa. Depois de ter identificado um nó de problema, pode tomar várias medidas no mesmo:

- **Reiniciar o nó** [(REST.NET))](https://docs.microsoft.com/rest/api/batchservice/computenode/reboot)  |  [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.reboot)

    Reiniciar o nó pode, por vezes, limpar problemas latentes, como processos bloqueados ou falhados. Se a sua piscina utilizar uma tarefa inicial ou se o seu trabalho utilizar uma tarefa de preparação de trabalho, eles são executados quando o nó recomeça.
- **Recriar imagem do nó** ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Isto reinstala o sistema operativo no nó. Tal como acontece com o reinício de um nó, as tarefas de início e as tarefas de preparação da tarefa são novamente executadas depois de ter sido recriada a imagem do nó.
- **Remover o nó do conjunto** ([REST](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations))

    Por vezes, é necessário remover completamente o nó do conjunto.
- **Desativar o agendamento de tarefas no nó** ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/disablescheduling) | [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Esta ação coloca o nó offline de forma eficaz, para que não lhe sejam atribuídas mais tarefas, mas permite que o nó permaneça em execução e no conjunto. Isto permite-lhe investigar melhor a causa das falhas sem perder os dados da tarefa com falhas e sem que o nó cause mais falhas nas tarefas. Por exemplo, pode desativar o agendamento de tarefas no nó e, em seguida, iniciar sessão remotamente para examinar os registos de eventos do nó ou realizar outras ações de resolução de problemas. Depois de terminar a sua investigação, pode então voltar a colocar o nó on-line, permitindo o agendamento de tarefas ([REST](https://docs.microsoft.com/rest/api/batchservice/computenode/enablescheduling)  |  [.NET,](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)ou realizar uma das outras ações discutidas anteriormente.

> [!IMPORTANT]
> Com as ações acima descritas, o youc pode especificar como as tarefas atualmente em execução no nó são manuseadas quando executa a ação. Por exemplo, quando desativar o agendamento de tarefas num nó com a biblioteca de cliente .NET do Batch, pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) para especificar se pretende **Terminar** as tarefas em execução, **Recolocá-las na fila** para agendamento noutros nós ou permitir que as tarefas em execução sejam concluídas antes de realizar a ação (**TaskCompletion**).

## <a name="retry-after-errors"></a>Voltar a tentar após erros

As APIs do Lote irão notificá-lo se houver uma falha. Todos podem ser julgados novamente, e todos incluem um manipulador global de retry para esse fim. É melhor usar este mecanismo incorporado.

Depois de uma falha, deve esperar um pouco (alguns segundos entre tentativas) antes de tentar novamente. Se voltar a tentar com demasiada frequência ou muito rapidamente, o manipulador de retry irá acelerar.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a verificar se há erros de [piscina e nó.](batch-pool-node-error-checking.md)
- Aprenda a verificar se há erros de [trabalho e tarefas.](batch-job-task-error-checking.md)
- Reveja a lista de [Códigos](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)de Erro e Estado do Lote .
