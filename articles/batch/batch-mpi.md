---
title: Usar tarefas de várias instâncias para executar aplicativos MPI – lote do Azure | Microsoft Docs
description: Saiba como executar aplicativos MPI (interface de transmissão de mensagens) usando o tipo de tarefa de várias instâncias no lote do Azure.
services: batch
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd58a18b4926d911df8493670ccd7da97708e075
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029678"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Usar tarefas de várias instâncias para executar aplicativos MPI (interface de transmissão de mensagens) no lote

As tarefas de várias instâncias permitem executar uma tarefa do lote do Azure em vários nós de computação simultaneamente. Essas tarefas permitem cenários de computação de alto desempenho como aplicativos MPI (interface de transmissão de mensagens) no lote. Neste artigo, você aprenderá a executar tarefas de várias instâncias usando a biblioteca [.net do lote][api_net] .

> [!NOTE]
> Embora os exemplos neste artigo se concentrem em .NET do lote, MS-MPI e nós de computação do Windows, os conceitos de tarefas de várias instâncias abordados aqui são aplicáveis a outras plataformas e tecnologias (Python e Intel MPI em nós Linux, por exemplo).
>
>

## <a name="multi-instance-task-overview"></a>Visão geral da tarefa de várias instâncias
No lote, cada tarefa normalmente é executada em um único nó de computação – você envia várias tarefas para um trabalho e o serviço de lote agenda cada tarefa para execução em um nó. No entanto, ao definir **as configurações de várias instâncias**de uma tarefa, você informa ao lote para criar uma tarefa principal e várias subtarefas que são executadas em vários nós.

![Visão geral da tarefa de várias instâncias][1]

Quando você envia uma tarefa com configurações de várias instâncias para um trabalho, o lote executa várias etapas exclusivas para tarefas de várias instâncias:

1. O serviço de lote cria uma **principal** e várias **subtarefas** com base nas configurações de várias instâncias. O número total de tarefas (primária mais todas as subtarefas) corresponde ao número de **instâncias** (nós de computação) que você especifica nas configurações de várias instâncias.
2. O lote designa um dos nós de computação como o **mestre**e agenda a tarefa principal para ser executada no mestre. Ele agenda as subtarefas a serem executadas no restante dos nós de computação alocados para a tarefa de várias instâncias, uma subtarefa por nó.
3. A primária e todas as subtarefas baixam quaisquer **arquivos de recursos comuns** que você especificar nas configurações de várias instâncias.
4. Depois que os arquivos de recursos comuns tiverem sido baixados, a primária e as subtarefas executarão o **comando de coordenação** especificado nas configurações de várias instâncias. O comando de coordenação normalmente é usado para preparar nós para executar a tarefa. Isso pode incluir a inicialização de serviços em segundo plano (como o `smpd.exe`da [Microsoft MPI][msmpi_msdn]) e a verificação de que os nós estão prontos para processar mensagens entre nós.
5. A tarefa principal executa o **comando do aplicativo** no nó mestre *após* o comando de coordenação ter sido concluído com êxito pela subtarefas primárias e todas. O comando de aplicativo é a linha de comando da própria tarefa de várias instâncias e é executado somente pela tarefa principal. Em uma solução baseada em [MS-MPI][msmpi_msdn], é aqui que você executa o aplicativo habilitado para MPI usando `mpiexec.exe`.

> [!NOTE]
> Embora seja funcionalmente distinto, a "tarefa de várias instâncias" não é um tipo de tarefa exclusivo como [StartTask][net_starttask] ou [JobPreparationTask][net_jobprep]. A tarefa de várias instâncias é simplesmente uma tarefa de lote padrão ([CloudTask][net_task] no .net do lote) cujas configurações de várias instâncias foram configuradas. Neste artigo, nos referimos a isso como a **tarefa de várias instâncias**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias
As tarefas de várias instâncias exigem um pool com a **comunicação entre nós habilitada**e com a **execução de tarefa simultânea desabilitada**. Para desabilitar a execução de tarefas simultâneas, defina a propriedade [CloudPool. MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) como 1.

> [!NOTE]
> O lote [limita](batch-quota-limit.md#pool-size-limits) o tamanho de um pool que tem comunicação entre nós habilitada.


Este trecho de código mostra como criar um pool para tarefas de várias instâncias usando a biblioteca .NET do lote.

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
> Se você tentar executar uma tarefa de várias instâncias em um pool com comunicação entre nós desabilitada ou com um valor de *maxTasksPerNode* maior que 1, a tarefa nunca será agendada, ela permanecerá indefinidamente no estado "ativo". 


### <a name="use-a-starttask-to-install-mpi"></a>Usar um StartTask para instalar o MPI
Para executar aplicativos MPI com uma tarefa de várias instâncias, primeiro você precisa instalar uma implementação MPI (MS-MPI ou Intel MPI, por exemplo) nos nós de computação no pool. Esse é um bom momento para usar um [StartTask][net_starttask], que é executado sempre que um nó ingressa em um pool ou é reiniciado. Esse trecho de código cria um StartTask que especifica o pacote de instalação do MS-MPI como um [arquivo de recurso][net_resourcefile]. A linha de comando da tarefa inicial é executada Depois que o arquivo de recurso é baixado para o nó. Nesse caso, a linha de comando executa uma instalação autônoma do MS-MPI.

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
Quando você escolhe um [tamanho compatível com RDMA](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , como A9 para os nós de computação em seu pool do lote, seu aplicativo MPI pode aproveitar a rede RDMA (acesso remoto direto à memória) de alto desempenho e baixa latência do Azure.

Procure os tamanhos especificados como "compatível com RDMA" nos seguintes artigos:

* Pools de **CloudServiceConfiguration**

  * [Tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md) (somente Windows)
* Pools de **VirtualMachineConfiguration**

  * [Tamanhos das máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Tamanhos das máquinas virtuais no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Para aproveitar o RDMA em [nós de computação do Linux](batch-linux-nodes.md), você deve usar o **Intel MPI** nos nós. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa de várias instâncias com o .NET do lote
Agora que já abordamos os requisitos de pool e a instalação do pacote MPI, vamos criar a tarefa de várias instâncias. Neste trecho, criamos um [CloudTask][net_task]padrão e, em seguida, configuramos sua propriedade [MultiInstanceSettings][net_multiinstance_prop] . Como mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa diferente, mas uma tarefa de lote padrão configurada com configurações de várias instâncias.

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

## <a name="primary-task-and-subtasks"></a>Tarefa principal e subtarefas
Ao criar as configurações de várias instâncias para uma tarefa, você especifica o número de nós de computação que devem executar a tarefa. Quando você envia a tarefa para um trabalho, o serviço de lote cria uma tarefa **principal** e **subtarefas** suficientes que juntos correspondem ao número de nós especificado.

Essas tarefas recebem uma ID de número inteiro no intervalo de 0 a *numberOfInstances* -1. A tarefa com ID 0 é a tarefa principal e todas as outras IDs são subtarefas. Por exemplo, se você criar as seguintes configurações de várias instâncias para uma tarefa, a tarefa principal teria uma ID de 0, e as subtarefas teriam IDs de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó mestre
Quando você envia uma tarefa de várias instâncias, o serviço de lote designa um dos nós de computação como o nó "mestre" e agenda a tarefa principal a ser executada no nó mestre. As subtarefas são agendadas para serem executadas no restante dos nós alocados para a tarefa de várias instâncias.

## <a name="coordination-command"></a>Comando de coordenação
O **comando de coordenação** é executado tanto pela primária quanto por subtarefas.

A invocação do comando de coordenação é o bloqueio – o lote não executa o comando do aplicativo até que o comando de coordenação seja retornado com êxito para todas as subtarefas. Portanto, o comando de coordenação deve iniciar todos os serviços em segundo plano necessários, verificar se eles estão prontos para uso e, em seguida, sair. Por exemplo, esse comando de coordenação para uma solução usando MS-MPI versão 7 inicia o serviço SMPD no nó e, em seguida, encerra:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe o uso de `start` neste comando de coordenação. Isso é necessário porque o aplicativo `smpd.exe` não retorna imediatamente após a execução. Sem o uso do comando [Start][cmd_start] , esse comando de coordenação não retornaria e, portanto, bloquearia a execução do comando do aplicativo.

## <a name="application-command"></a>Comando de aplicativo
Depois que a tarefa principal e todas as subtarefas concluírem a execução do comando de coordenação, a linha de comando da tarefa de várias instâncias será executada *apenas*pela tarefa principal. Chamamos isso de **comando do aplicativo** para distingui-lo do comando de coordenação.

Para aplicativos MS-MPI, use o comando de aplicativo para executar seu aplicativo habilitado para MPI com `mpiexec.exe`. Por exemplo, aqui está um comando de aplicativo para uma solução usando o MS-MPI versão 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Como o `mpiexec.exe` do MS-MPI usa a variável `CCP_NODES` por padrão (consulte [variáveis de ambiente](#environment-variables)), a linha de comando do aplicativo de exemplo acima a exclui.
>
>

## <a name="environment-variables"></a>Variáveis de ambiente
O lote cria várias [variáveis de ambiente][msdn_env_var] específicas para tarefas de várias instâncias nos nós de computação alocados para uma tarefa de várias instâncias. Suas linhas de comando de coordenação e de aplicativo podem referenciar essas variáveis de ambiente, assim como os scripts e programas que eles executam.

As seguintes variáveis de ambiente são criadas pelo serviço de lote para uso por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre essas e outras variáveis de ambiente do nó de computação do lote, incluindo seu conteúdo e visibilidade, consulte [variáveis de ambiente do nó de computação][msdn_env_var].

> [!TIP]
> O exemplo de código do Linux MPI do lote contém um exemplo de como várias dessas variáveis de ambiente podem ser usadas. O script de bash [de coordenação-cmd][coord_cmd_example] baixa o aplicativo comum e os arquivos de entrada do armazenamento do Azure, habilita um compartilhamento NFS (sistema de arquivos de rede) no nó mestre e configura os outros nós alocados para a tarefa de várias instâncias como clientes NFS.
>
>

## <a name="resource-files"></a>Arquivos de recurso
Há dois conjuntos de arquivos de recursos a serem considerados para tarefas de várias instâncias: **arquivos de recursos comuns** que *todas as* tarefas baixam (primárias e subtarefas) e os **arquivos de recursos** especificados para a própria tarefa de várias instâncias, que *somente a tarefa principal* baixa.

Você pode especificar um ou mais **arquivos de recursos comuns** nas configurações de várias instâncias para uma tarefa. Esses arquivos de recursos comuns são baixados do [armazenamento do Azure](../storage/common/storage-introduction.md) para o **diretório compartilhado da tarefa** de cada nó pelas subtarefas primárias e todas. Você pode acessar o diretório compartilhado de tarefa de linhas de comando de aplicativo e coordenação usando a variável de ambiente `AZ_BATCH_TASK_SHARED_DIR`. O caminho de `AZ_BATCH_TASK_SHARED_DIR` é idêntico em todos os nós alocados para a tarefa de várias instâncias, assim você pode compartilhar um único comando de coordenação entre as subtarefas primária e todas. O lote não "compartilha" o diretório em um sentido de acesso remoto, mas você pode usá-lo como um ponto de montagem ou compartilhamento, conforme mencionado anteriormente na dica sobre variáveis de ambiente.

Os arquivos de recurso que você especifica para a tarefa de várias instâncias em si são baixados para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`, por padrão. Conforme mencionado, ao contrário dos arquivos de recursos comuns, somente a tarefa principal baixa os arquivos de recursos especificados para a própria tarefa de várias instâncias.

> [!IMPORTANT]
> Sempre use as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para se referir a esses diretórios em suas linhas de comando. Não tente construir os caminhos manualmente.
>
>

## <a name="task-lifetime"></a>Tempo de vida da tarefa
O tempo de vida da tarefa principal controla o tempo de vida de toda a tarefa de várias instâncias. Quando o primário é encerrado, todas as subtarefas são encerradas. O código de saída do primário é o código de saída da tarefa e, portanto, é usado para determinar o êxito ou a falha da tarefa para finalidades de repetição.

Se qualquer uma das subtarefas falhar, sair com um código de retorno diferente de zero, por exemplo, toda a tarefa de várias instâncias falhará. A tarefa de várias instâncias é então encerrada e repetida, até seu limite de repetição.

Quando você exclui uma tarefa de várias instâncias, a primária e todas as subtarefas também são excluídas pelo serviço de lote. Todos os diretórios de subtarefas e seus arquivos são excluídos dos nós de computação, assim como para uma tarefa padrão.

[TaskConstraints][net_taskconstraints] para uma tarefa de várias instâncias, como as propriedades [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]e [retentiontime][net_taskconstraint_retention] , são respeitadas como são para uma tarefa padrão e aplicam-se à primária e a todas as subtarefas. No entanto, se você alterar a propriedade [Retentiontime][net_taskconstraint_retention] depois de adicionar a tarefa de várias instâncias ao trabalho, essa alteração será aplicada somente à tarefa principal. Todas as subtarefas continuam a usar o [Retentiontime][net_taskconstraint_retention]original.

A lista de tarefas recentes de um nó de computação refletirá a ID de uma subtarefa se a tarefa recente tiver sido parte de uma tarefa de várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre subtarefas
Para obter informações sobre subtarefas usando a biblioteca .NET do lote, chame o método [CloudTask. ListSubtasks][net_task_listsubtasks] . Esse método retorna informações sobre todas as subtarefas e informações sobre o nó de computação que executou as tarefas. A partir dessas informações, você pode determinar o diretório raiz de cada subtarefa, a ID do pool, seu estado atual, o código de saída e muito mais. Você pode usar essas informações em combinação com o método [PoolOperations. Getnodefile][poolops_getnodefile] para obter os arquivos da subtarefa. Observe que esse método não retorna informações para a tarefa principal (ID 0).

> [!NOTE]
> Salvo indicação em contrário, os métodos .NET do lote que operam no próprio [CloudTask][net_task] de várias instâncias se aplicam *somente* à tarefa principal. Por exemplo, quando você chama o método [CloudTask. ListNodeFiles][net_task_listnodefiles] em uma tarefa de várias instâncias, somente os arquivos da tarefa principal são retornados.
>
>

O trecho de código a seguir mostra como obter informações sobre a subtarefa, bem como solicitar o conteúdo do arquivo dos nós em que eles foram executados.

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
O exemplo de código [MultiInstanceTasks][github_mpi] no GitHub demonstra como usar uma tarefa de várias instâncias para executar um aplicativo [MS-MPI][msmpi_msdn] em nós de computação do lote. Siga as etapas em [preparação](#preparation) e [execução](#execution) para executar o exemplo.

### <a name="preparation"></a>Preparação
1. Siga as duas primeiras etapas em [como compilar e executar um programa MS-MPI simples][msmpi_howto]. Isso atende aos pré-requisitos para a etapa a seguir.
2. Crie uma versão de *lançamento* do programa MPI de exemplo [MPIHelloWorld][helloworld_proj] . Este é o programa que será executado em nós de computação pela tarefa de várias instâncias.
3. Crie um arquivo ZIP contendo `MPIHelloWorld.exe` (que você criou a etapa 2) e `MSMpiSetup.exe` (que você baixou a etapa 1). Você carregará esse arquivo zip como um pacote de aplicativos na próxima etapa.
4. Use o [portal do Azure][portal] para criar um [aplicativo](batch-application-packages.md) do lote chamado "MPIHelloWorld" e especifique o arquivo zip que você criou na etapa anterior como a versão "1,0" do pacote de aplicativos. Consulte [carregar e gerenciar aplicativos](batch-application-packages.md#upload-and-manage-applications) para obter mais informações.

> [!TIP]
> Crie uma versão de *lançamento* do `MPIHelloWorld.exe` para que você não precise incluir dependências adicionais (por exemplo, `msvcp140d.dll` ou `vcruntime140d.dll`) no seu pacote de aplicativos.
>
>

### <a name="execution"></a>Execução
1. Baixe o [Azure-batch-Samples][github_samples_zip] do github.
2. Abra a **solução** MultiInstanceTasks no Visual Studio 2019. O arquivo da solução `MultiInstanceTasks.sln` está localizado em:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Insira suas credenciais de conta de armazenamento e lote em `AccountSettings.settings` no projeto **Microsoft. Azure. Batch. Samples. Common** .
4. **Compile e execute** a solução MultiInstanceTasks para executar o aplicativo de exemplo MPI em nós de computação em um pool do lote.
5. *Opcional*: Use o [portal do Azure][portal] ou [batch Explorer][batch_labs] para examinar o pool de exemplo, o trabalho e a tarefa ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de excluir os recursos.

> [!TIP]
> Você pode baixar o [Visual Studio Community][visual_studio] gratuitamente se não tiver o Visual Studio.
>
>

A saída de `MultiInstanceTasks.exe` é semelhante ao seguinte:

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
* O blog do Microsoft HPC & equipe do lote do Azure discute o [suporte a MPI para Linux no lote do Azure][blog_mpi_linux]e inclui informações sobre como usar o [OpenFOAM][openfoam] com o lote. Você pode encontrar exemplos de código do Python para o [exemplo OpenFOAM no GitHub][github_mpi].
* Saiba como [criar pools de nós de computação do Linux](batch-linux-nodes.md) para uso em suas soluções MPI do lote do Azure.

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
