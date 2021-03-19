---
title: Criar tarefas para preparar & trabalhos completos em nóns de computação
description: Utilize tarefas de preparação ao nível do trabalho para minimizar a transferência de dados para os nós computacional do Azure Batch e liberte tarefas para limpeza de nó no final do trabalho.
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919459"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Executar tarefas de preparação de emprego e de libertação de emprego nos nosm de computação batch

 Um trabalho do Azure Batch requer frequentemente alguma forma de configuração antes de as suas tarefas serem executadas, e manutenção pós-trabalho quando as suas tarefas estão concluídas. Poderá necessitar de baixar dados comuns de entrada de tarefas para os seus nós de computação ou enviar dados de saída de tarefas para O Azure Storage após o fim do trabalho. Pode utilizar tarefas **de preparação** de emprego e **de libertação de empregos** para realizar estas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são tarefas de preparação e libertação de empregos?
Antes de executar as tarefas de um trabalho, a tarefa de preparação de trabalho funciona em todos os nós de computação programados para executar pelo menos uma tarefa. Uma vez concluído o trabalho, a tarefa de libertação de emprego funciona em cada nó na piscina que executou pelo menos uma tarefa. Tal como acontece com as tarefas normais do Lote, pode especificar uma linha de comando a invocar quando uma tarefa de preparação ou libertação de trabalho é executada.

As tarefas de preparação e libertação de trabalho oferecem funcionalidades familiares de tarefas do Batch, tais como descarregamento de ficheiros ( ficheiros de[recursos),][net_job_prep_resourcefiles]execução elevada, variáveis ambientais personalizadas, duração máxima de execução, contagem de repetição e tempo de retenção de ficheiros.

Nas seguintes secções, aprenderá a utilizar as [aulas jobPreparationTask][net_job_prep] e [JobReleaseTask encontradas][net_job_release] na biblioteca [Batch .NET.][api_net]

> [!TIP]
> As tarefas de preparação e libertação de empregos são especialmente úteis em ambientes de "pool partilhado", em que um conjunto de nós computacional persiste entre as corridas de trabalho e é usado por muitos empregos.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usar tarefas de preparação e libertação de empregos
As tarefas de preparação de emprego e de libertação de empregos são um bom ajuste para as seguintes situações:

**Baixar dados de tarefas comuns**

Os trabalhos em lote requerem frequentemente um conjunto comum de dados como entrada para as tarefas do trabalho. Por exemplo, nos cálculos diários de análise de risco, os dados de mercado são específicos do trabalho, mas comuns a todas as tarefas no trabalho. Estes dados de mercado, muitas vezes vários gigabytes de tamanho, devem ser descarregados para cada nó de computação apenas uma vez para que qualquer tarefa que executa no nó possa usá-lo. Utilize uma **tarefa de preparação** de trabalho para transferir estes dados para cada nó antes da execução de outras tarefas do trabalho.

**Eliminar saída de trabalho e tarefa**

Num ambiente de "pool compartilhado", onde os nós de cálculo de uma piscina não são desativados entre empregos, poderá ser necessário apagar dados de trabalho entre corridas. Pode precisar de conservar o espaço do disco nos nós ou satisfazer as políticas de segurança da sua organização. Utilize uma **tarefa de libertação de emprego** para eliminar dados que foram descarregados por uma tarefa de preparação de emprego, ou gerados durante a execução de tarefas.

**Retenção de registos**

É possível que queira manter uma cópia dos ficheiros de registo que as suas tarefas geram, ou talvez desrespeitam ficheiros que podem ser gerados por aplicações falhadas. Utilize uma **tarefa de libertação de emprego** nesses casos para comprimir e enviar estes dados para uma conta de Armazenamento [Azure.][azure_storage]

> [!TIP]
> Outra forma de persistir registos e outros dados de saída de trabalho e tarefa é utilizar a biblioteca de [convenções de ficheiros Azure Batch.](batch-task-output.md)
>
>

## <a name="job-preparation-task"></a>Tarefa de preparação de emprego


Antes da execução das tarefas de um trabalho, Batch executa a tarefa de preparação de trabalho em cada nó de computação programado para executar uma tarefa. Por predefinição, Batch aguarda que a tarefa de preparação do trabalho seja concluída antes de executar as tarefas programadas para executar no nó. No entanto, pode configurar o serviço para não esperar. Se o nó recomeçar, a tarefa de preparação do trabalho volta a funcionar. Também pode desativar este comportamento. Se tiver um emprego com uma tarefa de preparação de emprego e uma tarefa de gestor de emprego configurada, a tarefa de preparação de emprego funciona antes da tarefa de gerente de emprego, assim como faz para todas as outras tarefas. A tarefa de preparação de emprego sempre funciona primeiro.

A tarefa de preparação do trabalho é executada apenas com nós programados para executar uma tarefa. Isto impede a execução desnecessária de uma tarefa de preparação no caso de não ser atribuída uma tarefa a um nó. Isto pode ocorrer quando o número de tarefas para um trabalho é inferior ao número de nós numa piscina. Aplica-se também quando a [execução simultânea](batch-parallel-node-tasks.md) da tarefa é ativada, o que deixa alguns nós inativos se a contagem de tarefas for inferior à total possível de tarefas simultâneas. Ao não executar a tarefa de preparação de trabalho em nós ociosos, você pode gastar menos dinheiro em taxas de transferência de dados.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] difere do [CloudPool.StartTask][pool_starttask] no que JobPreparationTask executa no início de cada trabalho, enquanto o StartTask executa apenas quando um nó de computação se junta a uma piscina ou reinicia.
>


>## <a name="job-release-task"></a>Tarefa de libertação de emprego

Uma vez que um trabalho é marcado como concluído, a tarefa de libertação de trabalho é executada em cada nó na piscina que executou pelo menos uma tarefa. Marca-se um trabalho como concluído através da emissão de um pedido de rescisão. O serviço Batch define então o estado de trabalho para *terminar,* termina quaisquer tarefas ativas ou em execução associadas ao trabalho, e executa a tarefa de libertação de emprego. O trabalho passa então para o estado *concluído.*

> [!NOTE]
> A supressão de emprego também executa a tarefa de libertação de emprego. No entanto, se um trabalho já tiver sido encerrado, a tarefa de libertação não será executada uma segunda vez se o trabalho for posteriormente eliminado.

As tarefas de libertação de postos de trabalho podem ser executadas durante um máximo de 15 minutos antes de serem encerradas pelo serviço Batch. Para mais informações, consulte a [documentação de referência](/rest/api/batchservice/job/add#jobreleasetask)da API REST .
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefas de preparação e lançamento de emprego com Batch .NET
Para utilizar uma tarefa de preparação de emprego, atribua um objeto [JobPreparationTask][net_job_prep] à propriedade [CloudJob.JobPreparationTask][net_job_prep_cloudjob] do seu trabalho. Da mesma forma, inicialize uma [JobReleaseTask][net_job_release] e atribua-a à propriedade [CloudJob.JobReleaseTask][net_job_prep_cloudjob] do seu trabalho para definir a tarefa de libertação do trabalho.

Neste código, `myBatchClient` é um exemplo de [BatchClient,][net_batch_client]e `myPool` é uma piscina existente dentro da conta Batch.

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

Como mencionado anteriormente, a tarefa de libertação é executada quando um trabalho é encerrado ou eliminado. Terminar um trabalho com [JobOperations.TerminateJobAsync][net_job_terminate]. Excluir um trabalho com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente, termina ou apaga um trabalho quando as suas tarefas estão concluídas, ou quando foi atingido um tempo limite que definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Amostra de código no GitHub
Para ver a preparação de emprego e as tarefas de lançamento em ação, consulte o projeto de amostra [JobPrepRelease][job_prep_release_sample] no GitHub. Esta aplicação de consola faz o seguinte:

1. Cria uma piscina com dois nós.
2. Cria um trabalho com preparação de emprego, libertação e tarefas padrão.
3. Executa a tarefa de preparação do trabalho, que primeiro escreve o nó ID para um ficheiro de texto no diretório "compartilhado" de um nó.
4. Executa uma tarefa em cada nó que escreve o seu ID de tarefa para o mesmo ficheiro de texto.
5. Uma vez concluídas todas as tarefas (ou o tempo limite é atingido), imprime o conteúdo do ficheiro de texto de cada nó para a consola.
6. Quando o trabalho estiver concluído, executa a tarefa de desbloqueio de trabalho para eliminar o ficheiro do nó.
7. Imprime os códigos de saída da preparação do trabalho e liberta tarefas para cada nó em que executaram.
8. Pausa na execução para permitir a confirmação da eliminação de trabalho e/ou piscina.

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
> Devido à criação variável e à hora de início dos nós numa nova piscina (alguns nós estão prontos para tarefas antes de outros), poderá ver uma saída diferente. Especificamente, porque as tarefas são completas rapidamente, um dos nós da piscina pode executar todas as tarefas do trabalho. Se isto ocorrer, notará que as tarefas de preparação e libertação do trabalho não existem para o nó que executou nenhuma tarefa.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecione as tarefas de preparação e libertação de empregos no portal Azure
Ao executar a aplicação de amostra, pode utilizar o [portal Azure][portal] para visualizar as propriedades da função e as suas tarefas, ou até mesmo descarregar o ficheiro de texto partilhado que é modificado pelas tarefas do trabalho.

A imagem abaixo mostra a **lâmina de tarefas de preparação** no portal Azure após uma execução da aplicação da amostra. Navegue para as propriedades *JobPrepReleaseSampleJob* depois de concluídas as suas tarefas (mas antes de apagar o seu trabalho e piscina) e clique em **tarefas de preparação** ou **de lançamento** para ver as suas propriedades.

![Propriedades de preparação de emprego no portal Azure][1]

## <a name="next-steps"></a>Passos seguintes
### <a name="application-packages"></a>Pacotes de aplicações
Além da tarefa de preparação de trabalho, também pode utilizar a funcionalidade de pacotes de [aplicação](batch-application-packages.md) do Batch para preparar os nós de computação para a execução de tarefas. Esta funcionalidade é especialmente útil para a implementação de aplicações que não requerem a execução de um instalador, aplicações que contenham muitos (100+) ficheiros ou aplicações que requerem um controlo rigoroso da versão.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicações e dados de encenação
Este post do fórum msdn fornece uma visão geral de vários métodos de preparação dos seus nosmos para executar tarefas:

[Instalação de aplicações e dados de paragem nos nosm de computação batch][forum_post]

Escrito por um dos membros da equipa do Azure Batch, discute várias técnicas que pode usar para implementar aplicações e dados para calcular nós.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
