---
title: Criar tarefas para preparar e completar trabalhos em nós de computação - Lote Azure
description: Use tarefas de preparação no nível do trabalho para minimizar a transferência de dados para nós de computação do lote do Azure e liberar tarefas para limpeza de nó na conclusão do trabalho.
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: jushiman
ms.custom: seodec18
ms.openlocfilehash: ed3b96ff7f1117b1185159cfa4e4b03aa80d6b02
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935040"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Executar tarefas de preparação e liberação de trabalho em nós de computação do lote

 Um trabalho do lote do Azure geralmente requer alguma forma de configuração antes que suas tarefas sejam executadas e manutenção após o trabalho quando suas tarefas forem concluídas. Talvez seja necessário baixar dados de entrada de tarefa comuns para seus nós de computação ou carregar dados de saída de tarefa no armazenamento do Azure após a conclusão do trabalho. Você pode usar as tarefas de **preparação de trabalho** e de **liberação** de trabalho para executar essas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são tarefas de preparação e liberação de trabalho?
Antes de executar as tarefas de um trabalho, a tarefa de preparação de trabalho é executada em todos os nós de computação agendados para executar pelo menos uma tarefa. Depois que o trabalho for concluído, a tarefa de liberação de trabalho será executada em cada nó no pool que executou pelo menos uma tarefa. Assim como acontece com as tarefas de lote normais, você pode especificar uma linha de comando a ser invocada quando uma tarefa de preparação ou de liberação de trabalho for executada.

As tarefas de preparação e liberação do trabalho oferecem recursos de tarefa do lote conhecidos, como download de arquivo ([arquivos de recurso][net_job_prep_resourcefiles]), execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção do arquivo.

Nas seções a seguir, você aprenderá a usar as classes [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] encontradas na biblioteca [.net do lote][api_net] .

> [!TIP]
> As tarefas de preparação e liberação de trabalho são especialmente úteis em ambientes de "pool compartilhado", em que um pool de nós de computação persiste entre as execuções de trabalho e é usado por muitos trabalhos.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usar tarefas de preparação e liberação de trabalho
As tarefas de preparação de trabalho e de liberação de trabalho são uma boa opção para as seguintes situações:

**Baixar dados comuns da tarefa**

Os trabalhos em lotes geralmente exigem um conjunto comum de dados como entrada para as tarefas do trabalho. Por exemplo, em cálculos de análise de risco diário, os dados do mercado são específicos do trabalho, embora comuns a todas as tarefas no trabalho. Esses dados de mercado, geralmente vários gigabytes de tamanho, devem ser baixados para cada nó de computação apenas uma vez, para que qualquer tarefa executada no nó possa usá-lo. Use uma **tarefa de preparação de trabalho** para baixar esses dados para cada nó antes da execução das outras tarefas do trabalho.

**Excluir saída de trabalho e tarefa**

Em um ambiente de "pool compartilhado", no qual os nós de computação de um pool não são encerrados entre os trabalhos, talvez seja necessário excluir os dados do trabalho entre as execuções. Talvez seja necessário conservar o espaço em disco nos nós ou atender às políticas de segurança de sua organização. Use uma **tarefa de liberação de trabalho** para excluir dados que foram baixados por uma tarefa de preparação de trabalho ou gerados durante a execução da tarefa.

**Retenção de log**

Talvez você queira manter uma cópia dos arquivos de log que suas tarefas geram ou talvez arquivos de despejo de memória que possam ser gerados por aplicativos com falha. Use uma **tarefa de liberação de trabalho** nesses casos para compactar e carregar esses dados em uma conta de [armazenamento do Azure][azure_storage] .

> [!TIP]
> Outra maneira de persistir logs e outros dados de saída de trabalho e tarefa é usar a biblioteca de [convenções de arquivo do lote do Azure](batch-task-output.md) .
> 
> 

## <a name="job-preparation-task"></a>Tarefa de preparação do trabalho
Antes da execução das tarefas de um trabalho, o lote executa a tarefa de preparação de trabalho em cada nó de computação agendado para executar uma tarefa. Por padrão, o serviço de lote aguarda a conclusão da tarefa de preparação do trabalho antes de executar as tarefas agendadas para execução no nó. No entanto, você pode configurar o serviço para não aguardar. Se o nó for reiniciado, a tarefa de preparação do trabalho será executada novamente, mas você também poderá desabilitar esse comportamento.

A tarefa de preparação de trabalho é executada somente em nós que estão agendados para executar uma tarefa. Isso impede a execução desnecessária de uma tarefa de preparação no caso de um nó não ser atribuído a uma tarefa. Isso pode ocorrer quando o número de tarefas de um trabalho é menor que o número de nós em um pool. Ele também se aplica quando a [execução de tarefas simultâneas](batch-parallel-node-tasks.md) está habilitada, o que deixa alguns nós ociosos se a contagem de tarefas é menor do que o total possível de tarefas simultâneas. Ao não executar a tarefa de preparação de trabalho em nós ociosos, você pode gastar menos dinheiro em encargos de transferência de dados.

> [!NOTE]
> O [JobPreparationTask][net_job_prep_cloudjob] é diferente de [CloudPool. StartTask][pool_starttask] , pois o JobPreparationTask é executado no início de cada trabalho, enquanto o StartTask é executado somente quando um nó de computação ingressa primeiro em um pool ou é reiniciado.
> 
> 

## <a name="job-release-task"></a>Tarefa de liberação de trabalho
Quando um trabalho é marcado como concluído, a tarefa de liberação de trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Você marca um trabalho como concluído emitindo uma solicitação de encerramento. Em seguida, o serviço de lote define o estado do trabalho como *encerrando*, encerra todas as tarefas ativas ou em execução associadas ao trabalho e executa a tarefa de liberação do trabalho. Em seguida, o trabalho passa para o estado *concluído* .

> [!NOTE]
> A exclusão de trabalhos também executa a tarefa de liberação de trabalho. No entanto, se um trabalho já tiver sido encerrado, a tarefa de liberação não será executada uma segunda vez se o trabalho for excluído posteriormente.

Tarefas de liberação de trabalhos podem ser executadas por um máximo de 15 minutos antes de serem encerradas pelo serviço de lote. Para obter mais informações, consulte a [documentação de referência da API REST](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefas de preparação e versão do trabalho com .NET do lote
Para usar uma tarefa de preparação de trabalho, atribua um objeto [JobPreparationTask][net_job_prep] à propriedade [CloudJob. JobPreparationTask][net_job_prep_cloudjob] do seu trabalho. Da mesma forma, inicialize um [JobReleaseTask][net_job_release] e atribua-o à propriedade [CloudJob. JobReleaseTask][net_job_prep_cloudjob] do seu trabalho para definir a tarefa de liberação do trabalho.

Neste trecho de código, `myBatchClient` é uma instância de [BatchClient][net_batch_client]e `myPool` é um pool existente dentro da conta do lote.

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

Como mencionado anteriormente, a tarefa de liberação é executada quando um trabalho é encerrado ou excluído. Encerrar um trabalho com [JobOperations. TerminateJobAsync][net_job_terminate]. Exclua um trabalho com [JobOperations. DeleteJobAsync][net_job_delete]. Normalmente, você encerra ou exclui um trabalho quando suas tarefas são concluídas ou quando um tempo limite que você definiu foi atingido.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub
Para ver as tarefas de preparação e liberação de trabalho em ação, confira o projeto de exemplo [JobPrepRelease][job_prep_release_sample] no github. Esse aplicativo de console faz o seguinte:

1. Cria um pool com dois nós.
2. Cria um trabalho com preparação de trabalho, versão e tarefas padrão.
3. Executa a tarefa de preparação do trabalho, que primeiro grava a ID do nó em um arquivo de texto no diretório "compartilhado" de um nó.
4. Executa uma tarefa em cada nó que grava sua ID de tarefa no mesmo arquivo de texto.
5. Depois que todas as tarefas forem concluídas (ou o tempo limite for atingido), o imprime o conteúdo do arquivo de texto de cada nó no console.
6. Quando o trabalho for concluído, o executará a tarefa de liberação do trabalho para excluir o arquivo do nó.
7. Imprime os códigos de saída das tarefas de preparação e liberação de trabalho para cada nó no qual eles são executados.
8. Pausa a execução para permitir a confirmação de exclusão de trabalho e/ou pool.

A saída do aplicativo de exemplo é semelhante ao seguinte:

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
> Devido à criação da variável e à hora de início dos nós em um novo pool (alguns nós estão prontos para tarefas antes de outros), você poderá ver uma saída diferente. Especificamente, como as tarefas são concluídas rapidamente, um dos nós do pool pode executar todas as tarefas do trabalho. Se isso ocorrer, você observará que as tarefas de preparação e liberação de trabalho não existem para o nó que não executou tarefas.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecione as tarefas de preparação e liberação do trabalho no portal do Azure
Ao executar o aplicativo de exemplo, você pode usar o [portal do Azure][portal] para exibir as propriedades do trabalho e suas tarefas, ou até mesmo baixar o arquivo de texto compartilhado que é modificado pelas tarefas do trabalho.

A captura de tela abaixo mostra a **folha tarefas de preparação** no portal do Azure após uma execução do aplicativo de exemplo. Navegue até as propriedades *JobPrepReleaseSampleJob* depois que suas tarefas forem concluídas (mas antes de excluir seu trabalho e pool) e clique em **tarefas de preparação** ou tarefas de **liberação** para exibir suas propriedades.

![Propriedades de preparação do trabalho no portal do Azure][1]

## <a name="next-steps"></a>Passos seguintes
### <a name="application-packages"></a>Pacotes de aplicações
Além da tarefa de preparação do trabalho, você também pode usar o recurso [pacotes de aplicativos](batch-application-packages.md) do lote para preparar os nós de computação para a execução da tarefa. Esse recurso é especialmente útil para implantar aplicativos que não exigem a execução de um instalador, aplicativos que contêm muitos arquivos (mais de 100) ou aplicativos que exigem controle de versão estrito.

### <a name="installing-applications-and-staging-data"></a>Instalando aplicativos e preparando dados
Esta postagem do fórum do MSDN fornece uma visão geral de vários métodos para preparar seus nós para executar tarefas:

[Instalando aplicativos e preparando dados em nós de computação do lote][forum_post]

Escrito por um dos membros da equipe do lote do Azure, ele aborda várias técnicas que você pode usar para implantar aplicativos e dados em nós de computação.

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
