---
title: Utilize tarefas multi-instâncias para executar aplicações MPI
description: Saiba como executar aplicações de Interface de Passagem de Mensagens (MPI) utilizando o tipo de tarefa de várias instâncias em Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389405"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Utilize tarefas de várias instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) em Batch

As tarefas de várias instâncias permitem executar uma tarefa do Azure Batch em múltiplos nós de computação simultaneamente. Estas tarefas permitem cenários de computação de alto desempenho como aplicações de Interface de Passagem de Mensagens (MPI) em Batch. Neste artigo, aprende-se a executar tarefas de várias instâncias utilizando a biblioteca [Batch .NET.](/dotnet/api/microsoft.azure.batch)

> [!NOTE]
> Embora os exemplos deste artigo se centrem nos nós de cálculo Batch .NET, MS-MPI e Windows compute, os conceitos de tarefas de várias instâncias aqui discutidos aplicam-se a outras plataformas e tecnologias (Python e Intel MPI em nós Linux, por exemplo).

## <a name="multi-instance-task-overview"></a>Visão geral da tarefa em várias instâncias

Em Batch, cada tarefa é normalmente executada num único nó de computação-- você submete várias tarefas a um trabalho, e o serviço Batch agenda cada tarefa para execução em um nó. No entanto, ao configurar **as definições de várias instâncias** de uma tarefa, diga ao Batch para criar uma tarefa primária e vários subtarefas que são depois executados em múltiplos nós.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagrama mostrando uma visão geral das definições de vários casos.":::

Quando submete uma tarefa com configurações de várias instâncias a um trabalho, o Batch executa vários passos exclusivos para tarefas multi-instâncias:

1. O serviço Batch cria uma **subtask** **primária** e várias com base nas definições de vários casos. O número total de tarefas (primárias mais todas as subtarefas) corresponde ao número de **casos** (nó de computação) que especifica nas definições de vários casos.
2. Batch designa um dos nós computatórios como o **mestre,** e agenda a tarefa principal a executar no mestre. Ele agenda os subtarefas para executar sobre o restante dos nós de computação atribuídos à tarefa multi-instância, um subtask por nó.
3. As subtascos primárias e todas as subtasks descarregam quaisquer **ficheiros de recursos comuns** que especifique nas definições de vários casos.
4. Após o descarregamento dos ficheiros de recursos comuns, as subtasks primárias e subtasks executam o comando de **coordenação** que especifica nas definições de vários casos. O comando de coordenação é normalmente utilizado para preparar nós para executar a tarefa. Isto pode incluir o início de serviços de fundo (como [o microsoft MPI)](/message-passing-interface/microsoft-mpi) `smpd.exe` e verificar se os nós estão prontos para processar mensagens inter-nosd.
5. A tarefa primária executa o comando de **aplicação** no nó principal depois de o comando de coordenação *ter* sido concluído com sucesso pelas subtarefas primárias e por todos os subtarefas. O comando de aplicação é a linha de comando da tarefa em si, e é executado apenas pela tarefa principal. Numa solução baseada em [MS-MPI,](/message-passing-interface/microsoft-mpi) é aqui que executa a sua aplicação ativada por MPI utilizando `mpiexec.exe` .

> [!NOTE]
> Embora seja funcionalmente distinto, a "tarefa multi-instância" não é um tipo de tarefa único como o [StartTask](/dotnet/api/microsoft.azure.batch.starttask) ou [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). A tarefa em várias instâncias é simplesmente uma tarefa padrão de Lote[(CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) em Batch .NET) cujas definições de vários instâncias foram configuradas. Neste artigo, referimo-nos a esta tarefa **multi-instância.**

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas multi-instâncias

As tarefas multi-instância requerem um pool com **comunicação inter-nódoa ativada,** e com **execução de tarefas simultânea desativada**. Para desativar a execução de tarefas simultâneas, deslove a propriedade [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) para 1.

> [!NOTE]
> O lote [limita](batch-quota-limit.md#pool-size-limits) o tamanho de uma piscina que tem comunicação inter-node ativada.

Este corte de código mostra como criar uma piscina para tarefas de vários instâncias utilizando a biblioteca Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Se tentar executar uma tarefa de várias instâncias num pool com comunicação internade desativada, ou com um valor *de tarefaSlotsPerNode* superior a 1, a tarefa nunca é agendada -- permanece indefinidamente no estado "ativo".

### <a name="use-a-starttask-to-install-mpi"></a>Use um StartTask para instalar MPI

Para executar aplicações MPI com uma tarefa multi-instância, primeiro precisa instalar uma implementação de MPI (MS-MPI ou Intel MPI, por exemplo) nos nós de cálculo na piscina. Esta é uma boa altura para usar um [StartTask,](/dotnet/api/microsoft.azure.batch.starttask)que executa sempre que um nó une uma piscina, ou é reiniciado. Este snippet de código cria um StartTask que especifica o pacote de configuração MS-MPI como um [ficheiro de recursos](/dotnet/api/microsoft.azure.batch.resourcefile). A linha de comando da tarefa inicial é executada depois de o ficheiro de recursos ser descarregado para o nó. Neste caso, a linha de comando realiza uma instalação não acompanhada de MS-MPI.

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

Ao escolher um [tamanho capaz de RDMA,](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) como A9 para os nós de computação na sua piscina Batch, a sua aplicação MPI pode tirar partido da rede de acesso remoto de memória direta (RDMA) de alto desempenho e baixa latência da Azure.

Procure os tamanhos especificados como "RDMA capaz" em [tamanhos para máquinas virtuais em Azure](../virtual-machines/sizes.md) (para piscinas virtualMachineConfiguration) ou [Tamanhos para Serviços cloud](../cloud-services/cloud-services-sizes-specs.md) (para piscinas CloudServicesConfiguration).

> [!NOTE]
> Para tirar partido do RDMA nos [nós de computação Linux,](batch-linux-nodes.md)tem de utilizar **o Intel MPI** nos nós.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa de vários instâncias com o Batch .NET

Agora que cobrimos os requisitos da piscina e a instalação do pacote MPI, vamos criar a tarefa de vários instâncias. Neste corte, criamos um [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)padrão, em seguida, configuramos a sua propriedade [MultiInstanceSettings.](/dotnet/api/microsoft.azure.batch.cloudtask) Como mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distinto, mas uma tarefa padrão do Lote configurada com configurações de várias instâncias.

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

Quando cria as definições de várias instâncias para uma tarefa, especifica o número de nós de computação que estão para executar a tarefa. Quando submete a tarefa a um trabalho, o serviço Batch cria uma tarefa **primária** e **subtarefas suficientes** que, em conjunto, correspondem ao número de nós especificados.

Estas tarefas são atribuídas a um ID inteiro no intervalo de 0 a *númeroOfInstances* - 1. A tarefa com o ID 0 é a tarefa principal, e todos os outros IDs são subtarefas. Por exemplo, se criar as seguintes definições de várias instâncias para uma tarefa, a tarefa principal teria um ID de 0, e os subtarefas teriam IDs 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó-mestre

Quando submete uma tarefa de várias instâncias, o serviço Batch designa um dos nós de computação como o nó "mestre" e agenda a tarefa principal a executar no nó principal. Os subtarefas estão programados para executar sobre os restantes nós atribuídos à tarefa multi-instância.

## <a name="coordination-command"></a>Comando de coordenação

O **comando de coordenação** é executado tanto pelas primárias como pelos subtasks.

A invocação do comando de coordenação está a bloquear-- O Batch não executa o comando de aplicação até que o comando de coordenação tenha devolvido com sucesso para todos os subtasques. O comando de coordenação deve, portanto, iniciar os serviços de fundo necessários, verificar se estão prontos para serem utilizados e, em seguida, sair. Por exemplo, este comando de coordenação para uma solução utilizando a versão MS-MPI 7 inicia o serviço SMPD no nó e, em seguida, sai:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Note a utilização deste comando de `start` coordenação. Isto é necessário porque o `smpd.exe` pedido não regressa imediatamente após a execução. Sem a utilização do comando inicial, este comando de coordenação não regressaria, pelo que impediria o comando de aplicação de funcionar.

## <a name="application-command"></a>Comando de aplicação

Uma vez terminada a tarefa primária e todos os subtarefas que terminaram de executar o comando de coordenação, a linha de comando da tarefa de várias instâncias é executada *apenas* pela tarefa principal . Chamamos a isto o comando de **aplicação** para distingui-lo do comando de coordenação.

Para aplicações MS-MPI, utilize o comando de aplicação para executar a sua aplicação ativada por MPI com `mpiexec.exe` . Por exemplo, aqui está um comando de aplicação para uma solução utilizando a versão MS-MPI 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Como a MS-MPI `mpiexec.exe` utiliza a `CCP_NODES` variável por padrão (ver [variáveis ambientais),](#environment-variables)a linha de comando de aplicação de exemplo acima exclui-a.

## <a name="environment-variables"></a>Variáveis de ambiente

O Lote cria [várias variáveis ambientais específicas](batch-compute-node-environment-variables.md) para tarefas de várias instâncias nos nós de computação atribuídas a uma tarefa multi-instância. As suas linhas de comando de coordenação e aplicação podem referenciar estas variáveis ambientais, assim como os scripts e programas que executam.

As seguintes variáveis ambientais são criadas pelo serviço Batch para utilização por tarefas multi-instâncias:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre estas e as outras variáveis do ambiente do nó de computação batch, incluindo o seu conteúdo e visibilidade, consulte [variáveis do ambiente do nó computo](batch-compute-node-environment-variables.md).

> [!TIP]
> A amostra de [código MPI do Lote Linux](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) contém um exemplo de como várias destas variáveis ambientais podem ser utilizadas.

## <a name="resource-files"></a>Ficheiros de recursos

Existem dois conjuntos de ficheiros de recursos a ter em conta para tarefas de várias instâncias: **ficheiros de recursos comuns** que *todas as* tarefas descarregam (tanto primárias como subtarefas), e os **ficheiros** de recursos especificados para a tarefa em várias instâncias, que *apenas a* tarefa principal descarrega.

Pode especificar um ou mais **ficheiros de recursos comuns** nas definições de vários casos para uma tarefa. Estes ficheiros de recursos comuns são descarregados do [Azure Storage](../storage/common/storage-introduction.md) para a **tarefa partilhada** de cada nó pelos principais e todos os subtarefas. Pode aceder à tarefa partilhada a partir de linhas de comando de aplicação e coordenação utilizando a `AZ_BATCH_TASK_SHARED_DIR` variável ambiente. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em cada nó atribuído à tarefa de vários instâncias, pelo que pode partilhar um único comando de coordenação entre os subtarefas primários e todos os subtarefas. O lote não "partilha" o diretório num sentido de acesso remoto, mas pode usá-lo como um ponto de montagem ou partilha, como mencionado anteriormente na ponta sobre variáveis ambientais.

Os ficheiros de recursos que especifica para a tarefa em várias instâncias são descarregados para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR` por padrão. Como mencionado, em contraste com os ficheiros de recursos comuns, apenas a tarefa primária descarrega ficheiros de recursos especificados para a tarefa em várias instâncias.

> [!IMPORTANT]
> Utilize sempre as variáveis ambientais `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` consulte estes diretórios nas suas linhas de comando. Não tente construir os caminhos manualmente.

## <a name="task-lifetime"></a>Vida útil da tarefa

A vida útil da tarefa primária controla a vida útil de toda a tarefa de vários casos. Quando as primárias saem, todas as subtascos são encerradas. O código de saída do primário é o código de saída da tarefa, pelo que é utilizado para determinar o sucesso ou falha da tarefa para efeitos de retrip.

Se algum dos subtarefas falhar, saindo com um código de devolução não zero, por exemplo, toda a tarefa de várias instâncias falha. A tarefa em várias instâncias é então terminada e novamente julgada, até ao seu limite de retriação.

Quando elimina uma tarefa de várias instâncias, as subtarefas primárias e todas as subtarefas também são eliminadas pelo serviço Batch. Todos os diretórios de subtask e seus ficheiros são eliminados dos nós de computação, assim como para uma tarefa padrão.

[As propriedades TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) para uma tarefa multi-instância, como as propriedades [MaxTaskRetryCount,](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)e [RetentionTime,](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) são honradas como são para uma tarefa padrão, e aplicam-se às subtarefas primárias e a todas as subtarefas. No entanto, se alterar a propriedade RetentionTime depois de adicionar a tarefa de várias instâncias ao trabalho, esta alteração é aplicada apenas à tarefa principal, e todas as subtarefas continuam a utilizar o Tempo de Retenção original.

A lista de tarefas recente de um nó de cálculo reflete o ID de uma subtasque se a tarefa recente fizesse parte de uma tarefa multi-instância.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre subtasks

Para obter informações sobre subtascos utilizando a biblioteca Batch .NET, ligue para o método [CloudTask.ListSubtasksks.](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) Este método devolve informações sobre todos os subtarefas e informações sobre o nó de computação que executou as tarefas. A partir desta informação, você pode determinar o diretório de raiz de cada subtask, o ID da piscina, o seu estado atual, código de saída, e muito mais. Pode utilizar esta informação em combinação com o método [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) para obter os ficheiros do subtasco. Note que este método não devolve informações para a tarefa principal (ID 0).

> [!NOTE]
> Salvo indicação em contrário, os métodos Batch .NET que operam no [cloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) em várias instâncias aplicam-se *apenas* à tarefa principal. Por exemplo, quando liga para o método [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) numa tarefa de várias instâncias, apenas os ficheiros da tarefa principal são devolvidos.

O seguinte código snippet mostra como obter informações de subtask, bem como solicitar o conteúdo do ficheiro a partir dos nós em que executaram.

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

A amostra de código [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) no GitHub demonstra como utilizar uma tarefa de várias instâncias para executar uma aplicação [MS-MPI](/message-passing-interface/microsoft-mpi) nos nós de computação Batch. Siga os passos abaixo para executar a amostra.

### <a name="preparation"></a>Preparação

1. Descarregue os [instaladores MS-MPI SDK e Redist](/message-passing-interface/microsoft-mpi) e instale-os. Após a instalação, pode verificar se as variáveis ambientais MS-MPI foram definidas.
1. Construa uma versão *de lançamento* do programa [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) de amostra MPI. Este é o programa que será executado em nós de computação pela tarefa de vários instâncias.
1. Crie um ficheiro zip contendo `MPIHelloWorld.exe` (que construiu no passo 2) e `MSMpiSetup.exe` (que descarregou no passo 1). Você vai carregar este ficheiro zip como um pacote de aplicação no próximo passo.
1. Utilize o [portal Azure](https://portal.azure.com) para criar uma [aplicação](batch-application-packages.md) batch chamada "MPIHelloWorld", e especifique o ficheiro zip que criou no passo anterior como a versão "1.0" do pacote de aplicações. Consulte [o Upload e gere as aplicações](batch-application-packages.md#upload-and-manage-applications) para obter mais informações.

> [!TIP]
> A construção de uma versão *de Lançamento* garante que não tem de `MPIHelloWorld.exe` incluir quaisquer dependências adicionais (por exemplo, `msvcp140d.dll` `vcruntime140d.dll` ou) no seu pacote de aplicações.

### <a name="execution"></a>Execução

1. Descarregue o [ficheiro de amostras de lote de azul .zip](https://github.com/Azure/azure-batch-samples/archive/master.zip) do GitHub.
1. Abra a **solução** MultiInstanceTasks no Visual Studio 2019. O `MultiInstanceTasks.sln` ficheiro de solução está localizado em:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Insira as suas credenciais de conta de Lote e Armazenamento `AccountSettings.settings` no **projetoMicrosoft.Azure.Batch.Samples.Common.**
1. **Construa e execute** a solução MultiInstanceTasks para executar a aplicação de amostra MPI em nós computacional numa piscina de Lote.
1. *Opcional*: Utilize o [portal Azure](https://portal.azure.com) ou [o Batch Explorer](https://azure.github.io/BatchExplorer/) para examinar o pool de amostras, trabalho e tarefa ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de eliminar os recursos.

> [!TIP]
> Você pode baixar [visual Studio Community](https://visualstudio.microsoft.com/vs/community/) gratuitamente se ainda não tiver o Visual Studio.

A partir da produção `MultiInstanceTasks.exe` é semelhante ao seguinte:

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

- Leia mais sobre [o suporte do MPI para Linux em Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Saiba como [criar piscinas de nós de computação Linux](batch-linux-nodes.md) para utilização nas suas soluções MPI do Azure Batch.
