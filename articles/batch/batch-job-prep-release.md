---
title: Criar tarefas para preparar e completar trabalhos em nós de computação - Lote Azure
description: Utilize tarefas de preparação ao nível do trabalho para minimizar a transferência de dados para os nós de computação do Lote Azure e liberte tarefas para limpeza de nós no final do trabalho.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 7103daa4a943edfd8d05333f413245cebaf8f4af
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524261"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Executar tarefas de preparação de emprego e libertação de emprego em nódos de computação batch

 Um trabalho do Lote Azure requer frequentemente alguma forma de configuração antes de as suas tarefas serem executadas, e manutenção pós-trabalho quando as suas tarefas estão concluídas. Poderá ser necessário descarregar dados comuns de entrada de tarefas para os seus nós de computação ou enviar dados de saída de tarefas para o Armazenamento Azure após o trabalho estar concluído. Pode utilizar tarefas de **preparação** de emprego e **de libertação** de emprego para realizar estas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são tarefas de preparação e libertação de emprego?
Antes de as tarefas de um trabalho funcionarem, a tarefa de preparação de emprego funciona em todos os nós de computação programados para executar pelo menos uma tarefa. Uma vez concluído o trabalho, a tarefa de libertação de emprego funciona em cada nó na piscina que executou pelo menos uma tarefa. Tal como acontece com as tarefas normais do Lote, pode especificar uma linha de comando a ser invocada quando uma tarefa de preparação ou libertação de trabalho for executada.

As tarefas de preparação e lançamento de emprego oferecem funcionalidades familiares de tarefas do Lote, tais como o download de ficheiros[(ficheiros][net_job_prep_resourcefiles]de recursos), execução elevada, variáveis ambientais personalizadas, duração máxima de execução, contagem de retry e tempo de retenção de ficheiros.

Nas seguintes secções, aprenderá a utilizar as classes [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] encontradas na biblioteca [Batch .NET.][api_net]

> [!TIP]
> As tarefas de preparação e libertação de emprego são especialmente úteis em ambientes de "piscina partilhada", em que um conjunto de nós de computação persiste entre as corridas de emprego e é usado por muitos empregos.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usar tarefas de preparação e libertação de emprego
As tarefas de preparação de emprego e de libertação de emprego são um bom ajuste para as seguintes situações:

**Descarregue dados comuns de tarefas**

Os trabalhos em lote exigem frequentemente um conjunto comum de dados como contributo para as tarefas do trabalho. Por exemplo, nos cálculos diários de análise de riscos, os dados de mercado são específicos do emprego, mas comuns a todas as tarefas no trabalho. Estes dados de mercado, muitas vezes vários gigabytes de tamanho, devem ser descarregados para cada nó computacional apenas uma vez para que qualquer tarefa que corra no nó possa usá-lo. Utilize uma tarefa de **preparação** de trabalho para descarregar estes dados para cada nó antes da execução das outras tarefas do trabalho.

**Eliminar a saída de trabalho e tarefas**

Num ambiente de "piscina partilhada", onde os nós de computação de uma piscina não são desativados entre empregos, pode ser necessário apagar dados de emprego entre corridas. Você pode precisar de conservar espaço em disco nos nós, ou satisfazer as políticas de segurança da sua organização. Utilize uma tarefa de **libertação** de emprego para eliminar dados que foram descarregados por uma tarefa de preparação de emprego, ou gerados durante a execução da tarefa.

**Retenção de registos**

É possível que queira manter uma cópia dos ficheiros de registo que as suas tarefas geram, ou talvez ficheiros de despejo de falhas que possam ser gerados por aplicações falhadas. Utilize uma tarefa de **libertação** de emprego nesses casos para comprimir e fazer o upload destes dados para uma conta [de Armazenamento Azure.][azure_storage]

> [!TIP]
> Outra forma de persistir registos e outros dados de saída de trabalho e tarefas é utilizar a biblioteca de Convenções de [Ficheiros de Lote Azure.](batch-task-output.md)
> 
> 

## <a name="job-preparation-task"></a>Tarefa de preparação de emprego
Antes da execução das tarefas de um trabalho, o Batch executa a tarefa de preparação de trabalho em cada nó de cálculo que está programado para executar uma tarefa. Por padrão, o serviço De lote aguarda que a tarefa de preparação do trabalho seja concluída antes de executar as tarefas programadas para executar no nó. No entanto, pode configurar o serviço para não esperar. Se o nó recomeçar, a tarefa de preparação de trabalho volta a ser, mas também pode desativar este comportamento. Se tiver um trabalho com uma tarefa de preparação de emprego e uma tarefa de gestor de emprego configurada, a tarefa de preparação de emprego funciona antes da tarefa de gestor de emprego, tal como acontece com todas as outras tarefas. A tarefa de preparação de emprego é sempre a primeira.

A tarefa de preparação de emprego é executada apenas em nódos os que estão programados para executar uma tarefa. Isto impede a execução desnecessária de uma tarefa de preparação no caso de não ser atribuída uma tarefa a um nó. Isto pode ocorrer quando o número de tarefas para um trabalho é menor do que o número de nós numa piscina. Também se aplica quando a [execução de tarefas simultânea](batch-parallel-node-tasks.md) é ativada, o que deixa alguns nós inativos se a contagem de tarefas for inferior ao total possível de tarefas simultâneas. Ao não executar a tarefa de preparação de emprego em nós inativos, pode gastar menos dinheiro em taxas de transferência de dados.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] difere de [CloudPool.StartTask][pool_starttask] nesse JobPreparationTask executa no início de cada trabalho, enquanto o StartTask executa apenas quando um nó de computação se junta primeiro a uma piscina ou reinicia.
> 
> 

## <a name="job-release-task"></a>Tarefa de libertação de emprego
Uma vez que um trabalho é marcado como concluído, a tarefa de libertação de emprego é executada em cada nó na piscina que executou pelo menos uma tarefa. Marca um trabalho como concluído ao emitir um pedido de rescisão. O serviço Batch define então o estado de trabalho para *terminar,* encerra quaisquer tarefas ativas ou em execução associadas ao trabalho, e executa a tarefa de libertação de emprego. O trabalho passa-se então para o estado *completo.*

> [!NOTE]
> A eliminação do emprego também executa a tarefa de libertação de emprego. No entanto, se um trabalho já tiver sido rescindido, a tarefa de libertação não será executada uma segunda vez se o trabalho for posteriormente apagado.

As tarefas de libertação de empregos podem funcionar durante um máximo de 15 minutos antes de serem encerradas pelo serviço Batch. Para mais informações, consulte a documentação de referência da [API REST](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefas de preparação e lançamento de trabalho com Lote .NET
Para utilizar uma tarefa de preparação de emprego, atribua um objeto de Tarefa de [Preparação][net_job_prep] de Emprego à propriedade [CloudJob.JobPreparationTask][net_job_prep_cloudjob] do seu trabalho. Da mesma forma, inicialize uma [JobReleaseTask][net_job_release] e atribua-a à propriedade [CloudJob.JobReleaseTask][net_job_prep_cloudjob] do seu trabalho para definir a tarefa de lançamento do trabalho.

Neste código, `myBatchClient` é uma instância de [BatchClient,][net_batch_client]e `myPool` é um pool existente dentro da conta Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Como mencionado anteriormente, a tarefa de libertação é executada quando um trabalho é rescindido ou eliminado. Encerre um trabalho com [JobOperations.TerminateJobAsync][net_job_terminate]. Eliminar um trabalho com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente, termina ou elimina um trabalho quando as suas tarefas estão concluídas, ou quando foi atingido um prazo que definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Amostra de código no GitHub
Para ver tarefas de preparação e lançamento de emprego em ação, consulte o projeto de amostra [JobPrepRelease][job_prep_release_sample] no GitHub. Esta aplicação de consola faz o seguinte:

1. Cria uma piscina com dois nós.
2. Cria um trabalho com preparação de emprego, libertação e tarefas padrão.
3. Executa a tarefa de preparação de emprego, que primeiro escreve o ID do nó para um ficheiro de texto no diretório "partilhado" de um nó.
4. Executa uma tarefa em cada nó que escreve o seu ID de tarefa para o mesmo ficheiro de texto.
5. Uma vez concluídas todas as tarefas (ou o prazo é atingido), imprime o conteúdo do ficheiro de texto de cada nó para a consola.
6. Quando o trabalho estiver concluído, executa a tarefa de libertação de trabalho para eliminar o ficheiro do nó.
7. Imprime os códigos de saída da preparação do trabalho e liberta tarefas para cada nó em que executaram.
8. Pausa na execução para permitir a confirmação do trabalho e/ou eliminação do pool.

A saída da aplicação da amostra é semelhante à seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Devido à criação variável e à hora de início dos nós numa piscina nova (alguns nódos estão prontos para tarefas antes de outros), poderá ver uma saída diferente. Especificamente, porque as tarefas completam rapidamente, um dos nós da piscina pode executar todas as tarefas do trabalho. Se isso ocorrer, notará que as tarefas de preparação e libertação de trabalho não existem para o nó que não executou nenhuma tarefa.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecione as tarefas de preparação e libertação de emprego no portal Azure
Ao executar a aplicação da amostra, pode utilizar o [portal Azure][portal] para visualizar as propriedades do trabalho e as suas tarefas, ou até mesmo descarregar o ficheiro de texto partilhado que é modificado pelas tarefas do trabalho.

A imagem abaixo mostra a lâmina de tarefas de **preparação** no portal Azure após uma execução da aplicação da amostra. Navegue para as propriedades *JobPrepReleaseSampleJob* depois de as suas tarefas terem terminado (mas antes de apagar o seu trabalho e piscina) e clique em tarefas de **preparação** ou **tarefas** de lançamento para visualizar as suas propriedades.

![Propriedades de preparação de emprego no portal Azure][1]

## <a name="next-steps"></a>Passos seguintes
### <a name="application-packages"></a>Pacotes de aplicações
Além da tarefa de preparação de trabalho, também pode utilizar a funcionalidade de pacotes de [aplicação](batch-application-packages.md) do Batch para preparar nós de computação para execução de tarefas. Esta funcionalidade é especialmente útil para a implementação de aplicações que não requerem executar um instalador, aplicações que contenham muitos ficheiros (100+) ou aplicações que requeiram um controlo rigoroso da versão.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicações e dados de encenação
Este post do fórum MSDN fornece uma visão geral de vários métodos de preparação dos seus nós para tarefas de execução:

[Instalação de aplicações e dados de preparação em nódeos computacionais de Lote][forum_post]

Escrito por um dos membros da equipa do Lote Azure, discute várias técnicas que pode utilizar para implementar aplicações e dados para calcular nós.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
