---
title: Utilize tarefas de várias instâncias para executar aplicações de MPI
description: Saiba como executar aplicações de Interface de Passagem de Mensagens (MPI) utilizando o tipo de tarefa de várias instâncias no Lote Azure.
ms.topic: article
ms.date: 03/13/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad2b649c92716a807d577bb4f112d47d447545e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115980"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Utilize tarefas de várias instâncias para executar aplicações de interface de passagem de mensagens (MPI) em Lote

Tarefas de várias instâncias permitem-lhe executar simultaneamente uma tarefa do Lote Azure em vários nódos de cálculo. Estas tarefas permitem cenários de computação de alto desempenho, como aplicações de Interface de Passagem de Mensagem (MPI) no Lote. Neste artigo, aprende-se a executar tarefas em várias instâncias utilizando a biblioteca [Batch .NET.][api_net]

> [!NOTE]
> Embora os exemplos deste artigo se centrem em nóes de computação De Lote .NET, MS-MPI e Windows, os conceitos de tarefas de várias instâncias aqui discutidos são aplicáveis a outras plataformas e tecnologias (Python e Intel MPI em nós Linux, por exemplo).
>
>

## <a name="multi-instance-task-overview"></a>Visão geral de tarefas de várias instâncias
Em Batch, cada tarefa é normalmente executada num único nó computacional-- submete várias tarefas a um trabalho, e o serviço De lote agenda cada tarefa para execução num nó. No entanto, ao configurar **as definições de várias instâncias**de uma tarefa, diga ao Batch para criar uma tarefa primária e várias subtarefas que são executadas em vários nós.

![Visão geral de tarefas de várias instâncias][1]

Quando submete uma tarefa com configurações de várias instâncias para um trabalho, o Batch executa vários passos exclusivos de tarefas de várias instâncias:

1. O serviço Batch cria uma **subtarefa primária** e várias com base nas definições de **várias instâncias.** O número total de tarefas (primárias mais todas as subtarefas) corresponde ao número de **instâncias** (nós de computação) que especifica nas definições de várias instâncias.
2. O lote designa um dos nós computacionais como **o mestre,** e programa a tarefa principal para executar no mestre. Ele programa as subtarefas para executar no restante dos nós computacionais atribuídos à tarefa de várias instâncias, uma subtarefa por nó.
3. As principais e todas as subtarefas descarregam quaisquer **ficheiros de recursos comuns** que especifique nas definições de várias instâncias.
4. Depois de descarregados os ficheiros de recursos comuns, as principais e subtarefas executam o comando de **coordenação** que especifica nas definições de várias instâncias. O comando de coordenação é normalmente usado para preparar nódosos para executar a tarefa. Isto pode incluir o início de serviços `smpd.exe`de fundo (como o [MPI's][msmpi_msdn]da Microsoft) e verificar se os nós estão prontos para processar mensagens inter-nó.
5. A tarefa principal executa o comando de **aplicação** no nó principal *depois* de o comando de coordenação ter sido concluído com sucesso pelas primárias e todas as subtarefas. O comando de aplicação é a linha de comando da própria tarefa em várias instâncias, e é executado apenas pela tarefa principal. Numa solução baseada em [MS-MPI,][msmpi_msdn]é aqui que executa `mpiexec.exe`a sua aplicação ativada por MPI utilizando .

> [!NOTE]
> Embora seja funcionalmente distinto, a "tarefa de várias instâncias" não é um tipo de tarefa único como a Tarefa de [Preparação de Tarefas de Início][net_starttask] ou [JobPreparation .][net_jobprep] A tarefa de várias instâncias é simplesmente uma tarefa padrão do Lote[(CloudTask][net_task] in Batch .NET) cujas definições de várias instâncias foram configuradas. Neste artigo, referimo-nos a esta questão como uma **tarefa em várias instâncias.**
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias
As tarefas de várias instâncias requerem um pool com **comunicação inter-nó ativada,** e com execução de **tarefas simultânea desativada**. Para desativar a execução de tarefas simultânea, desative a propriedade [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) para 1.

> [!NOTE]
> O lote [limita](batch-quota-limit.md#pool-size-limits) o tamanho de uma piscina que tem comunicação inter-nó ativada.


Este fragmento de código mostra como criar uma piscina para tarefas de várias instâncias utilizando a biblioteca Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Se tentar executar uma tarefa em várias instâncias numa piscina com comunicação internade desativada, ou com um valor *maxTasksPerNode* superior a 1, a tarefa nunca é agendada -- permanece indefinidamente no estado "ativo". 


### <a name="use-a-starttask-to-install-mpi"></a>Utilize uma StartTask para instalar MPI
Para executar aplicações de MPI com uma tarefa multi-instância, é necessário primeiro instalar uma implementação de MPI (MS-MPI ou Intel MPI, por exemplo) nos nós de cálculo na piscina. Esta é uma boa altura para utilizar uma [StartTask,][net_starttask]que executa sempre que um nó se junta a uma piscina, ou é reiniciada. Este fragmento de código cria uma StartTask que especifica o pacote de configuração MS-MPI como ficheiro de [recurso][net_resourcefile]. A linha de comando da tarefa inicial é executada após o ficheiro de recursos ser descarregado para o nó. Neste caso, a linha de comando realiza uma instalação sem supervisão de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Acesso remoto à memória direta (RDMA)
Ao escolher um [tamanho capaz de RDMA,](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como O 9 para os nós de computação na sua piscina de Lote, a sua aplicação MPI pode tirar partido da rede de acesso à memória direta remota (RDMA) de alto desempenho e baixa latência do Azure.

Procure os tamanhos especificados como "RDMA capaz" nos seguintes artigos:

* **Piscinas cloudServiceConfiguration**

  * [Tamanhos para Serviços em Nuvem](../cloud-services/cloud-services-sizes-specs.md) (apenas janelas)
* **Piscinas de Configuração VirtualMachine**

  * [Tamanhos para máquinas virtuais em Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Tamanhos para máquinas virtuais em Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Para tirar partido do RDMA nos [nós computacionais do Linux,](batch-linux-nodes.md)deve utilizar o **Intel MPI** nos nós. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa de várias instâncias com o Batch .NET
Agora que cobrimos os requisitos da piscina e a instalação de pacotes MPI, vamos criar a tarefa em várias instâncias. Neste corte, criamos uma [CloudTask][net_task]padrão, e configuramos a sua propriedade [MultiInstânciaSettings.][net_multiinstance_prop] Como mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distinto, mas uma tarefa padrão do Lote configurada com configurações de várias instâncias.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Tarefa primária e subtarefas
Quando cria as definições de várias instâncias para uma tarefa, especifica o número de nós computacionais que devem executar a tarefa. Quando submete a tarefa a um trabalho, o serviço Batch cria uma tarefa **primária** e **subtarefas suficientes** que, em conjunto, correspondem ao número de nós que especificou.

Estas tarefas são atribuídas a um id inteiro na gama de 0 a *númeroOfInstances* - 1. A tarefa com o id 0 é a tarefa principal, e todos os outros ids são subtarefas. Por exemplo, se criar as seguintes definições em várias instâncias para uma tarefa, a tarefa principal teria um id de 0, e as subtarefas teriam ids 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó mestre
Quando submete uma tarefa em várias instâncias, o serviço Batch designa um dos nós computacionais como o nó "mestre", e programa a tarefa principal para executar no nó principal. As subtarefas estão programadas para executar no restante dos nódos atribuídos à tarefa em várias instâncias.

## <a name="coordination-command"></a>Comando de coordenação
O comando de **coordenação** é executado tanto pelas primárias como pelas subtarefas.

A invocação do comando de coordenação está a bloquear-- O lote não executa o comando de aplicação até que o comando de coordenação tenha regressado com sucesso para todas as subtarefas. O comando de coordenação deve, portanto, iniciar os serviços de fundo necessários, verificar se estão prontos para serem utilizados e, em seguida, sair. Por exemplo, este comando de coordenação para uma solução utilizando a versão 7 mS-MPI inicia o serviço SMPD no nó, em seguida, sai:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Note a `start` utilização neste comando de coordenação. Isto é necessário `smpd.exe` porque o pedido não regressa imediatamente após a execução. Sem a utilização do [comando][cmd_start] inicial, este comando de coordenação não regressaria, pelo que impediria o comando de aplicação de funcionar.

## <a name="application-command"></a>Comando de aplicação
Uma vez que a tarefa principal e todas as subtarefas tenham terminado de executar o comando de coordenação, a linha de comando da tarefa de várias instâncias é executada *apenas*pela tarefa principal . Chamamos a isto o comando de **aplicação** para distingui-lo do comando de coordenação.

Para aplicações MS-MPI, utilize o comando de aplicação `mpiexec.exe`para executar a sua aplicação ativada por MPI com . Por exemplo, aqui está um comando de aplicação para uma solução utilizando a versão 7 mS-MPI:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Uma vez que a `mpiexec.exe` MS-MPI utiliza a `CCP_NODES` variável por padrão (ver [variáveis ambientais),](#environment-variables)a linha de comando de aplicação de exemplo acima exclui-a.
>
>

## <a name="environment-variables"></a>Variáveis de ambiente
O lote cria [várias variáveis ambientais específicas][msdn_env_var] de tarefas de várias instâncias nos nódeos computacionais atribuídos a uma tarefa em várias instâncias. As linhas de comando de coordenação e aplicação podem fazer referência a estas variáveis ambientais, assim como os scripts e programas que executam.

As seguintes variáveis ambientais são criadas pelo serviço Batch para utilização por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para mais detalhes sobre estas e outras variáveis ambientais de némesis de computação batch, incluindo o seu conteúdo e visibilidade, consulte [variáveis ambientais computadas.][msdn_env_var]

> [!TIP]
> A amostra de código MPI batch Linux contém um exemplo de como várias destas variáveis ambientais podem ser usadas. O guião de [coordenação-cmd][coord_cmd_example] Bash descarrega aplicações comuns e ficheiros de entrada do Azure Storage, permite uma partilha do Sistema de Ficheiros de Rede (NFS) no nó principal, e confunde os outros nós atribuídos à tarefa em várias instâncias como clientes NFS.
>
>

## <a name="resource-files"></a>Ficheiros de recursos
Existem dois conjuntos de ficheiros de recursos a considerar para tarefas de várias instâncias: **ficheiros de recursos comuns** que *todas as* tarefas descarregam (primárias e subtarefas), e os **ficheiros** de recursos especificados para a própria tarefa em várias instâncias, que *apenas a* tarefa primária descarrega.

Pode especificar um ou mais **ficheiros de recursos comuns** nas definições de várias instâncias para uma tarefa. Estes ficheiros de recursos comuns são descarregados do [Azure Storage](../storage/common/storage-introduction.md) para a tarefa partilhada de cada nó **pelo diretório** principal e por todas as subtarefas. Pode aceder ao diretório partilhado de tarefas a `AZ_BATCH_TASK_SHARED_DIR` partir das linhas de comando de aplicação e coordenação utilizando a variável ambiental. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em cada nó atribuído à tarefa em várias instâncias, assim pode partilhar um único comando de coordenação entre as primárias e todas as subtarefas. O lote não "partilha" o diretório num sentido de acesso remoto, mas pode usá-lo como um ponto de montagem ou partilha, como mencionado anteriormente na dica sobre variáveis ambientais.

Os ficheiros de recursos que especifica para a tarefa em várias instâncias são descarregados para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`por padrão. Como mencionado, em contraste com os ficheiros de recursos comuns, apenas a tarefa primária descarrega ficheiros de recursos especificados para a própria tarefa em várias instâncias.

> [!IMPORTANT]
> Utilize sempre as `AZ_BATCH_TASK_SHARED_DIR` variáveis ambientais e `AZ_BATCH_TASK_WORKING_DIR` refira-se a estes diretórios nas suas linhas de comando. Não tente construir os caminhos manualmente.
>
>

## <a name="task-lifetime"></a>Vida útil da tarefa
A vida útil da tarefa primária controla a vida útil de toda a tarefa em várias instâncias. Quando as primárias saem, todas as subtarefas terminam. O código de saída do primário é o código de saída da tarefa, e é, portanto, utilizado para determinar o sucesso ou falha da tarefa para efeitos de retentativa.

Se alguma das subtarefas falhar, sair com um código de devolução não zero, por exemplo, toda a tarefa de várias instâncias falha. A tarefa em várias instâncias é então encerrada e novamente tentada, até ao seu limite de retry.

Quando elimina uma tarefa de várias instâncias, as principais e todas as subtarefas também são eliminadas pelo serviço 'Lote'. Todos os diretórios de subtarefas e seus ficheiros são eliminados dos nós computacionais, assim como para uma tarefa padrão.

[Os TaskConstraints][net_taskconstraints] para uma tarefa em várias instâncias, tais como as propriedades [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]e [RetentionTime,][net_taskconstraint_retention] são honrados como são para uma tarefa padrão, e aplicam-se às principais e todas as subtarefas. No entanto, se alterar a propriedade [RetentionTime][net_taskconstraint_retention] depois de adicionar a tarefa em várias instâncias ao trabalho, esta alteração é aplicada apenas à tarefa principal. Todas as subtarefas continuam a utilizar o Tempo de [Retenção][net_taskconstraint_retention]original .

A recente lista de tarefas de um nó computacional reflete a identificação de uma subtarefa se a tarefa recente fizesse parte de uma tarefa em várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre subtarefas
Para obter informações sobre subtarefas utilizando a biblioteca Batch .NET, ligue para o método [CloudTask.ListSubtasks.][net_task_listsubtasks] Este método devolve informações sobre todas as subtarefas e informações sobre o nó computacional que executou as tarefas. A partir desta informação, você pode determinar o diretório raiz de cada subtarefa, o id da piscina, seu estado atual, código de saída, e muito mais. Pode utilizar esta informação em combinação com o método [PoolOperations.GetNodeFile][poolops_getnodefile] para obter os ficheiros da subtarefa. Note que este método não devolve informações para a tarefa principal (id 0).

> [!NOTE]
> Salvo indicação em contrário, os métodos Batch .NET que operam na própria [CloudTask][net_task] em várias instâncias aplicam-se *apenas* à tarefa principal. Por exemplo, quando liga para o método [CloudTask.ListNodeFiles][net_task_listnodefiles] numa tarefa de várias instâncias, apenas os ficheiros da tarefa primária são devolvidos.
>
>

O seguinte código de corte mostra como obter informações de subtarefa, bem como solicitar conteúdo de ficheiro dos nós em que executaram.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemplo de código
A amostra de código [MultiInstanceTasks][github_mpi] no GitHub demonstra como usar uma tarefa de várias instâncias para executar uma aplicação [MS-MPI][msmpi_msdn] em nós de computação batch. Siga os passos de [Preparação](#preparation) e [Execução](#execution) para executar a amostra.

### <a name="preparation"></a>Preparação
1. Siga os dois primeiros passos em [Como compilar e executar um simples programa mS-MPI][msmpi_howto]. Isto satisfaz os pré-requisitos para o passo seguinte.
2. Construa uma versão de *Lançamento* do programa [MPIHelloWorld][helloworld_proj] sample MPI. Este é o programa que será executado em nós de computação pela tarefa em várias instâncias.
3. Crie um `MPIHelloWorld.exe` ficheiro zip contendo (que `MSMpiSetup.exe` construiu o passo 2) e (que descarregou o passo 1). Você vai carregar este ficheiro zip como um pacote de aplicação no próximo passo.
4. Utilize o [portal Azure][portal] para criar uma [aplicação](batch-application-packages.md) de Lote chamada "MPIHelloWorld", e especificar o ficheiro zip que criou no passo anterior como versão "1.0" do pacote de aplicações. Consulte [o Upload e gereas aplicações](batch-application-packages.md#upload-and-manage-applications) para obter mais informações.

> [!TIP]
> Construa uma versão *de Lançamento* para que não tenha de incluir `msvcp140d.dll` dependências adicionais (por exemplo, ou) `vcruntime140d.dll`no seu pacote de `MPIHelloWorld.exe` aplicações.
>
>

### <a name="execution"></a>Execução
1. Descarregue as [amostras de lote azul][github_samples_zip] do GitHub.
2. Abra a **solução** MultiInstanceTasks no Visual Studio 2019. O `MultiInstanceTasks.sln` ficheiro de solução está localizado em:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Insira as credenciais `AccountSettings.settings` da sua conta de Lote e Armazenamento no projeto **Microsoft.Azure.Batch.Samples.Common.**
4. **Construir e executar** a solução MultiInstânciaTasks para executar a aplicação da amostra MPI em nódos de cálculo numa piscina de Lote.
5. *Opcional:* Utilize o [portal Azure][portal] ou [o Batch Explorer][batch_labs] para examinar o conjunto de amostras, o trabalho e a tarefa ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de eliminar os recursos.

> [!TIP]
> Você pode baixar [Visual Studio Community][visual_studio] gratuitamente se você não tiver Visual Studio.
>
>

A `MultiInstanceTasks.exe` saída é semelhante à seguinte:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Passos seguintes
* O blog da Microsoft HPC & Azure Batch Team discute o suporte de [MPI para o Linux no Lote Azure,][blog_mpi_linux]e inclui informações sobre a utilização do [OpenFOAM][openfoam] com batch. Pode encontrar amostras de código Python para o [exemplo OpenFOAM no GitHub][github_mpi].
* Aprenda a [criar piscinas de nódos de computato Linux](batch-linux-nodes.md) para utilização nas suas soluções DE MPI do Lote Azure.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Visão geral de várias instâncias"
