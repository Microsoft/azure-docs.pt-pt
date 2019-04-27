---
title: Criar tarefas para preparar trabalhos e tarefas de conclua em nós de computação - Azure Batch | Documentos da Microsoft
description: Utilize tarefas de preparação de ao nível da tarefa para minimizar a transferência de dados para nós de computação do Azure Batch e tarefas de limpeza de nó após conclusão da tarefa de lançamento.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 02/27/2017
ms.date: 06/29/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776209"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Nós de computação de execução de tarefa de preparação e tarefas de lançamento no Batch

 Uma tarefa do Azure Batch muitas vezes requer alguma forma de configuração antes das respetivas tarefas são executadas e pós-tarefas manutenção quando as respetivas tarefas são concluídas. Poderá ter de transferir dados de entrada de tarefas comuns para os nós de computação ou carregar dados de saída da tarefa ao armazenamento do Azure após a conclusão da tarefa. Pode usar **preparação da tarefa** e **lançamento da tarefa** tarefas para executar estas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são a tarefa de preparação e tarefas de lançamento?
Antes de executam tarefas de um trabalho, a tarefa de preparação é executado em todos os nós de computação agendados para ser executado, pelo menos, uma tarefa. Depois da tarefa estiver concluída, a tarefa de lançamento da tarefa é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Tal como acontece com tarefas do Batch normal, pode especificar uma linha de comando a ser invocado quando uma tarefa de preparação ou de lançamento da tarefa é executada.

Tarefas de preparação e lançamento oferecem recursos de tarefa do Batch familiares, como a transferência de ficheiros ([arquivos de recursos][net_job_prep_resourcefiles]), elevados de execução, variáveis de ambiente personalizadas, duração de execução máxima, tente novamente Contagem e tempo de retenção de ficheiros.

Nas secções seguintes, irá aprender a utilizar o [JobPreparationTask] [ net_job_prep] e [JobReleaseTask] [ net_job_release] classes encontradas no [ .NET do batch] [ api_net] biblioteca.

> [!TIP]
> Tarefas de preparação e lançamento são especialmente úteis em ambientes de "agrupamento partilhado", em que um conjunto de nós de computação de persistir entre as execuções de tarefa e é utilizado por muitos trabalhos.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando utilizar a tarefa de preparação e tarefas de lançamento
Preparação de trabalho e tarefas de lançamento são uma boa opção nas seguintes situações:

**Transferir dados de tarefas comuns**

Tarefas de lote, muitas vezes, necessitam de um conjunto comum de dados como entrada para as tarefas do trabalho. Por exemplo, nos cálculos de análise de risco diária, dados de mercado são específico de tarefa, ainda é comum a todas as tarefas no trabalho. Estes dados de mercado, muitas vezes, vários gigabytes de tamanho, devem ser apenas uma vez transferidos para cada nó de computação para que qualquer tarefa que é executado no nó pode utilizá-lo. Utilize um **tarefa de preparação** para transferir estes dados a cada nó antes da execução da tarefa 's outras tarefas.

**Eliminar a saída de trabalhos e tarefas**

Num ambiente de "agrupamento partilhado", em que nós de computação de um agrupamento não estiverem desativados entre tarefas, terá de eliminar dados de trabalho entre as execuções. Poderá ter de poupar espaço em disco em nós ou cumprir as políticas de segurança da sua organização. Utilize um **tarefa de libertação** para eliminar dados que foi baixados por uma tarefa de preparação ou gerados durante a execução da tarefa.

**Retenção do registo**

Pode querer manter uma cópia de ficheiros de registo que suas tarefas geram, ou talvez de falha de ficheiros de informação que podem ser gerados por aplicações com falha. Utilize um **tarefa de libertação** nestes casos para comprimir e carregar estes dados para um [armazenamento do Azure] [ azure_storage] conta.

> [!TIP]
> Outra forma de manter os registos e outros trabalhos e tarefas de saída dados consiste em utilizar o [convenções de ficheiros do Azure Batch](batch-task-output.md) biblioteca.
> 
> 

## <a name="job-preparation-task"></a>Tarefa de preparação de Trabalho
Antes da execução de tarefas de um trabalho, o Batch executa a tarefa de preparação em cada nó de computação que esteja agendada para executar uma tarefa. Por predefinição, o serviço Batch aguarda que a tarefa de preparação ser concluída antes de executar as tarefas agendadas para serem executadas no nó. No entanto, pode configurar o serviço não de espera. Se o nó for reiniciado, a tarefa de preparação é executado novamente, mas também pode desativar esse comportamento.

A tarefa de preparação é executada apenas em nós que estão agendados para executar uma tarefa. Isto impede que a execução desnecessária de uma tarefa de preparação, no caso de um nó não está atribuído uma tarefa. Isto pode ocorrer quando o número de tarefas para uma tarefa é inferior ao número de nós num conjunto. Também se aplica quando [a execução da tarefa em simultâneo](batch-parallel-node-tasks.md) é ativado, que deixa a alguns if de inatividade de nós a contagem de tarefas é inferior a total possíveis tarefas simultâneas. Por não executar a tarefa de preparação em nós inativos, pode gastar menos dinheiro em custos de transferência de dados.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] difere [CloudPool.StartTask] [ pool_starttask] que JobPreparationTask executa no início de cada tarefa, ao passo que o StartTask é executado apenas quando um nó de computação em primeiro lugar é adicionado um conjunto ou for reiniciado.
> 
> 

## <a name="job-release-task"></a>Tarefa de lançamento de Trabalho
Depois de uma tarefa é marcada como concluída, a tarefa de libertação é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Marcar uma tarefa como concluída ao emitir um pedido para terminar. O serviço Batch, em seguida, define o estado da tarefa *terminar*termina todas as tarefas em execução ou Active Directory associadas à tarefa e é executada a tarefa de libertação. A tarefa, em seguida, passa para o *concluída* estado.

> [!NOTE]
> Eliminação da tarefa também executa a tarefa de libertação. No entanto, se uma tarefa já foi terminada, a tarefa de lançamento não é executada uma segunda vez se o trabalho mais tarde é eliminado.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Preparação da tarefa e tarefas com o Batch .NET de versão
Para utilizar uma tarefa de preparação, atribuir um [JobPreparationTask] [ net_job_prep] objeto para a sua tarefa [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] propriedade. Da mesma forma, inicializar um [JobReleaseTask] [ net_job_release] e atribuí-lo para a sua tarefa [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] propriedade a definir a tarefa tarefa de lançamento.

Neste trecho de código `myBatchClient` é uma instância de [BatchClient][net_batch_client], e `myPool` é um conjunto existente na conta do Batch.

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

Como mencionado anteriormente, o lançamento da tarefa é executado quando uma tarefa é terminada ou eliminada. Terminar uma tarefa com [joboperations. Terminatejobasync][net_job_terminate]. Eliminar uma tarefa com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente, encerrar ou eliminar uma tarefa quando as respetivas tarefas são concluídas ou quando for atingido o limite de tempo que definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub
Para ver a tarefa de preparação e lançamento tarefas em ação, veja a [JobPrepRelease] [ job_prep_release_sample] projeto de exemplo no GitHub. Esta aplicação de consola faz o seguinte:

1. Cria um conjunto com dois nós.
2. Cria uma tarefa com a tarefa de preparação, versão e tarefas padrão.
3. É executada a tarefa de preparação, que primeiro escreve o ID de nó num arquivo de texto no diretório de "partilhado" de um nó.
4. Executa uma tarefa em cada nó que escreve o respetivo ID de tarefa para o mesmo arquivo de texto.
5. Depois de todas as tarefas são concluídas (ou o tempo limite é alcançado), imprime o conteúdo do arquivo de texto de cada nó na consola.
6. Quando a tarefa estiver concluída, é executada a tarefa de lançamento de tarefa para eliminar o ficheiro a partir do nó.
7. Imprime códigos de saída das tarefas de preparação e lançamento de trabalho para cada nó no qual eles executados.
8. Execução de interrupções para permitir que a confirmação da eliminação de tarefa e/ou em conjunto.

Resultado da aplicação de exemplo é semelhante ao seguinte:

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
> Devido ao tempo de criação e começar a variável de nós de um conjunto novo (alguns nós estão prontos para tarefas antes de outras pessoas a maiúsculas e minúsculas), pode ver um resultado diferente. Especificamente, uma vez que as tarefas são concluídas rapidamente, um de nós do conjunto pode executar todas as tarefas da tarefa. Se isto ocorrer, irá reparar que a tarefa de preparação e não existem tarefas de lançamento para o nó que não existem tarefas executadas.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecionar a preparação de trabalho e tarefas de lançamento no portal do Azure
Quando executar o aplicativo de exemplo, pode utilizar o [portal do Azure] [ portal] para ver as propriedades do trabalho e as respetivas tarefas, ou até mesmo transferir o ficheiro de texto partilhado que é modificado pelas tarefas do trabalho.

A captura de ecrã abaixo mostra a **painel de tarefas de preparação** no portal do Azure após uma execução do exemplo de aplicativo. Navegue para o *JobPrepReleaseSampleJob* propriedades depois de concluíram as tarefas (mas antes de eliminar o seu trabalho e o conjunto) e clique em **tarefas de preparação** ou **versão tarefas**para ver as respetivas propriedades.

![Propriedades de preparação da tarefa no portal do Azure][1]

## <a name="next-steps"></a>Passos Seguintes
### <a name="application-packages"></a>Pacotes de aplicações
Além da tarefa de preparação, também pode utilizar o [pacotes de aplicações](batch-application-packages.md) funcionalidade do Batch para preparar nós de computação para a execução da tarefa. Esta funcionalidade é especialmente útil para implantação de aplicativos que não requerem a execução de um instalador, aplicações que contêm muitos (mais de 100) ficheiros ou aplicações que necessitam de controle de versão rigorosa.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste
Esta postagem do Fórum MSDN fornece uma descrição geral dos vários métodos de preparar os nós para a execução de tarefas:

[Instalar aplicações e dados em lote de teste de nós de computação][forum_post]

Escrito por um dos membros da equipa do Azure Batch, ele aborda várias técnicas que pode utilizar para implementar aplicações e dados em nós de computação.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://www.azure.cn/home/features/storage/
[portal]: https://portal.azure.cn
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

<!-- Update_Description: wording update -->