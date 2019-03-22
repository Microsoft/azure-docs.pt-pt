---
title: Utilizar tarefas de várias instâncias para executar aplicações MPI - Azure Batch | Documentos da Microsoft
description: Saiba como executar aplicações Message Passing Interface (MPI) com o tipo de tarefa de várias instâncias no Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fe75dabe098cf98f0c3c04d592a32d6a44cebf8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905026"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Utilizar tarefas de várias instâncias para executar aplicações Message Passing Interface (MPI) no Batch

Tarefas de várias instâncias permitem-lhe executar uma tarefa do Batch em vários nós de computação em simultâneo. Estas tarefas ativar cenários como a aplicações Message Passing Interface (MPI) no Batch de computação de alto desempenho. Neste artigo, irá aprender a executar tarefas de várias instâncias com o [.NET do Batch] [ api_net] biblioteca.

> [!NOTE]
> Embora os exemplos neste artigo se concentrar em .NET do Batch, MS-MPI, e nós de computação do Windows, os conceitos de tarefa de várias instâncias discutidos aqui são aplicáveis a outras plataformas e tecnologias (Python e Intel MPI em nós do Linux, por exemplo).
>
>

## <a name="multi-instance-task-overview"></a>Visão geral da tarefa de várias instâncias
No lote, cada tarefa normalmente é executado num único nó de computação – enviar várias tarefas para uma tarefa e o serviço Batch agenda cada tarefa para execução num nó. No entanto, ao configurar uma tarefa **definições de várias instâncias**, informe o Batch em vez disso, criar uma tarefa principal e várias subtarefas que, em seguida, são executadas em vários nós.

![Visão geral da tarefa de várias instâncias][1]

Ao submeter uma tarefa com definições de várias instâncias para uma tarefa, o Batch executa vários passos exclusivos para tarefas de várias instâncias:

1. O serviço Batch cria um **primário** e várias **subtarefas** com base nas configurações de várias instâncias. O número total de tarefas (principais além de todas as subtarefas) corresponde ao número de **instâncias** (nós de computação) especificados nas definições de várias instâncias.
2. Batch designa um de nós de computação como o **mestre**e agenda a principal tarefa a ser executadas a mestre. Ele agenda as subtarefas para executar o restante de nós de computação alocados para a tarefa de várias instâncias, uma subtarefa por nó.
3. O principal e todas as subtarefas transferir qualquer **ficheiros de recursos comuns** especificados nas definições de várias instâncias.
4. Depois do recurso comum os ficheiros foram transferidos, o principal e subtarefas de executar o **comandos de coordenação** especificados nas definições de várias instâncias. O comando de coordenação é normalmente utilizado para preparar nós para executar a tarefa. Isto pode incluir a partir de serviços em segundo plano (como [Microsoft MPI][msmpi_msdn]do `smpd.exe`) e verificar que estão prontos para processar mensagens do nó entre os nós.
5. A principal tarefa executa o **comando de aplicação** no nó principal *depois* o comando de coordenação foi concluído com êxito ao principal e todas as subtarefas. O comando de aplicativo é a linha de comando da tarefa de várias instâncias em si e é executado apenas pela tarefa principal. Num [MS-MPI][msmpi_msdn]-com base em solução, isso é onde executa a sua aplicação com capacidade de MPI com `mpiexec.exe`.

> [!NOTE]
> Embora isso seja funcionalmente distinto, "tarefa de várias instâncias" não é um tipo de tarefa exclusiva, como o [StartTask] [ net_starttask] ou [JobPreparationTask] [ net_jobprep]. A tarefa de várias instâncias é simplesmente uma tarefa de lote standard ([CloudTask] [ net_task] no .NET do Batch) foram configuradas cujas definições de várias instâncias. Neste artigo, nos Referimos a isso como o **tarefas de várias instâncias**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias
Um conjunto de com para realizar tarefas de várias instâncias **ativada a comunicação entre nós**e com **desativada a execução da tarefa em simultâneo**. Para desativar a execução da tarefa em simultâneo, defina o [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) propriedade como 1.

> [!NOTE]
> Batch [limites](batch-quota-limit.md#pool-size-limits) o tamanho de um conjunto que tenha ativada a comunicação entre nós.


Este fragmento de código mostra como criar um conjunto para tarefas de várias instâncias com a biblioteca .NET do Batch.

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
> Se tentar executar uma tarefa de várias instâncias num conjunto com a comunicação internós desativada, ou com um *maxTasksPerNode* valor superior a 1, a tarefa não está agendada – indefinidamente permanece no estado "ativo". 


### <a name="use-a-starttask-to-install-mpi"></a>Utilizar um StartTask para instalar o MPI
Para executar aplicações MPI com uma tarefa de várias instâncias, tem primeiro de instalar uma implementação de MPI (MS-MPI ou do Intel MPI, por exemplo) em nós de computação no conjunto. Este é um bom momento para utilizar um [StartTask][net_starttask], que é executado sempre que um nó é adicionado um conjunto ou reiniciado. Este fragmento de código cria um StartTask que especifica o pacote de configuração do MS-MPI como um [arquivo de recursos][net_resourcefile]. Linha de comandos da tarefa de início é executada após o arquivo de recurso é transferido para o nó. Neste caso, a linha de comandos executa uma instalação autónoma do MS-MPI.

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

### <a name="remote-direct-memory-access-rdma"></a>Acesso remoto direto à memória (RDMA)
Ao escolher uma [tamanho com capacidade RDMA](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como A9 para os nós de computação do conjunto do Batch, seu aplicativo de MPI pode tirar partido da rede de acesso (RDMA) de alto desempenho e de baixa latência memória direta remoto do Azure.

Procure os tamanhos especificados como "Com capacidade RDMA" nos seguintes artigos:

* **CloudServiceConfiguration** conjuntos

  * [Tamanhos de serviços Cloud](../cloud-services/cloud-services-sizes-specs.md) (apenas Windows)
* **VirtualMachineConfiguration** conjuntos

  * [Tamanhos de máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Tamanhos de máquinas virtuais no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Para tirar partido de RDMA [nós de computação do Linux](batch-linux-nodes.md), tem de utilizar **Intel MPI** em nós. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Crie uma tarefa de várias instâncias com .NET do Batch
Agora que já abordamos os requisitos de agrupamento e a instalação do pacote MPI, vamos criar a tarefa de várias instâncias. Neste fragmento, criamos uma norma [CloudTask][net_task], em seguida, configure seu [MultiInstanceSettings] [ net_multiinstance_prop] propriedade. Como mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distinto, mas uma tarefa de lote padrão configurado com definições de várias instâncias.

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

## <a name="primary-task-and-subtasks"></a>Principais tarefas e subtarefas
Quando cria as definições de várias instâncias de uma tarefa, especifica o número de nós de computação que está a executar a tarefa. Quando submete a tarefa a uma tarefa, o serviço Batch cria um **primário** tarefa e suficiente **subtarefas** que em conjunto corresponde ao número de nós que especificou.

Estas tarefas são atribuídas um id de número inteiro no intervalo de 0 para *numberOfInstances* - 1. A tarefa com o id de 0 é a principal tarefa e todos os outros ids são subtarefas. Por exemplo, se criar as seguintes definições de várias instâncias para uma tarefa, a principal tarefa seria ter um id de 0, e as subtarefas teria ids de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó principal
Quando submete uma tarefa de várias instâncias, o serviço Batch designa um de nós de computação como o nó "principal" bem como agendas a principal tarefa para executar no nó principal. As subtarefas são agendadas para serem executadas no restante de nós alocadas para a tarefa de várias instâncias.

## <a name="coordination-command"></a>Comandos de coordenação
O **comandos de coordenação** é executado pelo primário e subtasks.

A invocação do comando coordenação está a bloquear – Batch não é executado o comando de aplicativo até que o comando de coordenação devolveu com êxito para todas as subtarefas. O comando de coordenação, portanto, deve começar todos os serviços em segundo plano necessária, certifique-se de que estão prontos para uso e, em seguida, saia. Por exemplo, este comando de coordenação para uma solução com a versão de MS-MPI 7 inicia o serviço SMPD no nó e, em seguida, é encerrado:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe o uso de `start` neste comando de coordenação. Isto é necessário porque o `smpd.exe` aplicativo não retorna imediatamente após a execução. Sem o uso do [começar] [ cmd_start] de comando, este comando de coordenação não retornaria e, portanto, bloquearia o comando de aplicativo a execução.

## <a name="application-command"></a>Comando de aplicação
Assim que a principal tarefa e todas as subtarefas terminar de executar o comando de coordenação, linha de comandos da tarefa de várias instâncias é executada pela tarefa primária *apenas*. Vamos chamá-lo a **comando aplicação** para distingui-la a partir do comando de coordenação.

Para aplicativos de MS-MPI, utilize o comando de aplicativo para executar a aplicação com capacidade de MPI com `mpiexec.exe`. Por exemplo, eis um comando de aplicação para uma solução com o MS-MPI versão 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Porque MS-MPI `mpiexec.exe` utiliza o `CCP_NODES` variável por padrão (consulte [variáveis de ambiente](#environment-variables)) da linha de comandos da aplicação de exemplo acima exclui-lo.
>
>

## <a name="environment-variables"></a>Variáveis de ambiente
O batch cria vários [variáveis de ambiente] [ msdn_env_var] específicas para tarefas de várias instâncias em nós de computação alocados para uma tarefa de várias instâncias. As linhas de comandos de coordenação e a aplicação pode referenciar estas variáveis de ambiente, como podem os scripts e programas que elas são executadas.

As seguintes variáveis de ambiente são criadas pelo serviço Batch para utilização por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter os detalhes completos sobre esses e outro lote de computação variáveis de ambiente de nó, incluindo os respetivos conteúdos e a visibilidade, consulte [variáveis de ambiente de nó de computação][msdn_env_var].

> [!TIP]
> O exemplo de código do Batch Linux MPI contém um exemplo de como várias destas variáveis de ambiente podem ser utilizadas. O [coordenação cmd] [ coord_cmd_example] aplicação comum de downloads do script de Bash e entrada de ficheiros do armazenamento do Azure, permite que uma partilha de sistema de ficheiros de rede (NFS) no nó principal e configura os outros nós alocado para a tarefa de várias instâncias, como os clientes NFS.
>
>

## <a name="resource-files"></a>Ficheiros de recursos
Existem dois conjuntos de ficheiros de recursos a serem considerados para tarefas de várias instâncias: **ficheiros de recursos comuns** que *todos os* tarefas transferir (ambos os principal e subtarefas de) e o **dearquivosderecursos** especificado para várias instâncias de tarefas por si só, quais *apenas primário* downloads de tarefas.

Pode especificar um ou mais **ficheiros de recursos comuns** nas definições de várias instâncias para uma tarefa. Estes ficheiros de recursos comuns são transferidos a partir [armazenamento do Azure](../storage/common/storage-introduction.md) em cada nó **diretório partilhado de tarefa** pelo principal e todas as subtarefas. Pode aceder o diretório partilhado de tarefas da aplicação e a coordenação de linhas de comandos utilizando a `AZ_BATCH_TASK_SHARED_DIR` variável de ambiente. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em cada nó alocada para a tarefa de várias instâncias, portanto, pode partilhar um comando único coordenação entre a primária e todas as subtarefas. Batch não "partilha" o diretório num sentido de acesso remoto, mas pode utilizá-la como uma montagem ou partilhar ponto a como mencionado anteriormente na dica de variáveis de ambiente.

Arquivos de recursos que especificar para a tarefa de várias instâncias em si são transferidos para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`, por predefinição. Conforme mencionado, em contraste com arquivos de recursos comuns, a principal tarefa transfere os ficheiros de recursos especificados para a tarefa de várias instâncias propriamente dita.

> [!IMPORTANT]
> Utilize sempre as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para fazer referência a esses diretórios em suas linhas de comando. Não tente construir os caminhos manualmente.
>
>

## <a name="task-lifetime"></a>Duração de tarefa
O tempo de vida da tarefa principal controla a duração da tarefa todo várias instâncias. Quando o principal é fechado, todas as subtarefas são terminadas. O código de saída do primário é o código de saída da tarefa e, portanto, é utilizado para determinar o êxito ou falha da tarefa para fins de repetição.

Se alguma das subtarefas falhar, a sair com um código de retorno diferente de zero, por exemplo, a tarefa de toda instância multi falha. A tarefa de várias instâncias, em seguida, é encerrada e repetida, até o limite de tentativas.

Quando elimina uma tarefa de várias instâncias, o principal e todas as subtarefas também são eliminadas pelo serviço Batch. Todos os diretórios da subtarefa e respetivos ficheiros são eliminados dos nós de computação, tal como sucede com uma tarefa padrão.

[TaskConstraints] [ net_taskconstraints] para uma tarefa de várias instâncias, tais como a [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], e [RetentionTime] [ net_taskconstraint_retention] propriedades, são honradas que são para uma tarefa padrão, e aplicam-se para a primária e todas as subtarefas. No entanto, se alterar o [RetentionTime] [ net_taskconstraint_retention] propriedade depois de adicionar a tarefa de várias instâncias do trabalho, esta alteração é aplicada apenas a principal tarefa. Todas as subtarefas continuam a utilizar o original [RetentionTime][net_taskconstraint_retention].

Lista de tarefas recentes de um nó de computação reflete o id de uma subtarefa se a tarefa recente fazia parte de uma tarefa de várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre as subtarefas
Para obter informações sobre as subtarefas através da biblioteca .NET do Batch, chamar o [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Este método devolve informações sobre todas as subtarefas e as informações acerca do nó de computação que as tarefas executadas. Com essas informações, pode determinar o diretório de raiz de cada subtarefa, o id do conjunto, seu estado atual, código de saída e muito mais. Pode usar essas informações em combinação com o [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obter os ficheiros da subtarefa. Tenha em atenção que este método não devolve informações para a tarefa primária (id de 0).

> [!NOTE]
> A menos que indicado de outra forma, métodos de .NET do Batch que operam em várias instâncias [CloudTask] [ net_task] próprio aplicar *apenas* para a tarefa principal. Por exemplo, quando chama o [CloudTask.ListNodeFiles] [ net_task_listnodefiles] apenas os ficheiros da tarefa principal do método numa tarefa de várias instâncias, são devolvidos.
>
>

O fragmento de código seguinte mostra como obter informações de subtarefa, bem como solicitar o conteúdo do ficheiro de nós em que eles executados.

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
O [MultiInstanceTasks] [ github_mpi] código de exemplo no GitHub demonstra como utilizar uma tarefa de várias instâncias para executar uma [MS-MPI] [ msmpi_msdn] aplicação em Nós de computação do batch. Siga os passos em [preparação](#preparation) e [execução](#execution) para executar o exemplo.

### <a name="preparation"></a>Preparação
1. Siga os duas primeiras passos [como compilar e executar um programa simples do MS-MPI][msmpi_howto]. Isso satisfaz os pré-requisitos para o passo seguinte.
2. Criar uma *lançamento* versão dos [MPIHelloWorld] [ helloworld_proj] programa de MPI de exemplo. Este é o programa que será executado em nós de computação pela tarefa de várias instâncias.
3. Criar um ficheiro zip que contém `MPIHelloWorld.exe` (que criou o passo 2) e `MSMpiSetup.exe` (que transferiu passo 1). Deverá carregar este ficheiro zip como um pacote de aplicação no próximo passo.
4. Utilize o [portal do Azure] [ portal] para criar um lote [aplicação](batch-application-packages.md) chamado "MPIHelloWorld" e especifique o ficheiro zip que criou no passo anterior como "1.0" de versão dos pacote de aplicações. Ver [carregar e gerir aplicações](batch-application-packages.md#upload-and-manage-applications) para obter mais informações.

> [!TIP]
> Criar uma *lançamento* versão do `MPIHelloWorld.exe` para que não precisa incluir quaisquer dependências adicionais (por exemplo, `msvcp140d.dll` ou `vcruntime140d.dll`) no seu pacote de aplicação.
>
>

### <a name="execution"></a>Execução
1. Transfira o [azure-batch-samples] [ github_samples_zip] do GitHub.
2. Abra o MultiInstanceTasks **solução** no Visual Studio 2017. O `MultiInstanceTasks.sln` arquivo da solução está localizado em:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Introduza as suas credenciais de conta do Batch e armazenamento no `AccountSettings.settings` no **Samples** projeto.
4. **Criar e executar** a solução de MultiInstanceTasks para executar a MPI exemplo de aplicação em nós de computação no conjunto do Batch.
5. *Opcional*: Utilize o [portal do Azure] [ portal] ou [Explorador do Batch] [ batch_labs] para examinar o conjunto de exemplo, trabalhos e tarefas ("MultiInstanceSamplePool"," MultiInstanceSampleJob","MultiInstanceSampleTask") antes de eliminar os recursos.

> [!TIP]
> Pode baixar [Visual Studio Community] [ visual_studio] gratuitamente se não tiver o Visual Studio.
>
>

Saída de `MultiInstanceTasks.exe` é semelhante ao seguinte:

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

## <a name="next-steps"></a>Passos Seguintes
* Blog do Microsoft HPC e a equipa do Azure Batch aborda [MPI suporte para Linux no Azure Batch][blog_mpi_linux]e inclui informações sobre como utilizar [OpenFOAM] [ openfoam] com o Batch. Pode encontrar exemplos de código do Python para o [OpenFOAM exemplo no GitHub][github_mpi].
* Saiba como [criar conjuntos de nós de computação do Linux](batch-linux-nodes.md) para utilização nas suas soluções do Azure Batch MPI.

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

[1]: ./media/batch-mpi/batch_mpi_01.png "Descrição geral de várias instâncias"
