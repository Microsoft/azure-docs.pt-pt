---
title: Executar uma carga de trabalho paralela utilizando a API .NET
description: Tutorial – Transcodificar ficheiros de multimédia em paralelo com o ffmpeg no Azure Batch através da biblioteca de cliente .NET para o Batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 2ea68b8a6cf26db2e4ba440140cfa900cebbb4aa
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335660"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Tutorial: Executar uma carga de trabalho paralela com o Azure Batch através da API .NET

Utilize o Azure Batch para executar trabalhos de lote de computação de alto desempenho (HPC) e paralelos em larga escala de forma eficaz no Azure. Este tutorial guia-o por um exemplo C# de execução de uma carga de trabalho paralela com o Batch. Obteve mais informações sobre um fluxo de trabalho de aplicações do Batch comum e como interagir programaticamente com recursos do Batch e de Armazenamento. Saiba como:

> [!div class="checklist"]
> * Adicionar um pacote de aplicação à sua conta do Batch
> * Autenticar com contas do Batch e de Armazenamento
> * Carregar ficheiros de entrada para o Armazenamento
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Criar um trabalho e tarefas para processar os ficheiros de entrada
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

Neste tutorial, vai converter ficheiros de multimédia MP4 em paralelo com o formato MP3 através da ferramenta de código aberto [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017 ou posteriormente](https://www.visualstudio.com/vs), ou [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1) para Linux, macOS ou Windows.

* Uma conta do Batch e uma conta de Armazenamento do Microsoft Azure associada. Para criar estas contas, veja os inícios rápidos do Batch com o [portal do Azure](quick-create-portal.md) ou com a [CLI do Azure](quick-create-cli.md).

* [Versão do Windows 64-bit de ffmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-10-01-essentials_build.7z) (.zip). Transfira o ficheiro zip para o seu computador local. Para este tutorial, só precisa do ficheiro zip. Não tem de deszipar o ficheiro ou instalá-lo localmente.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-an-application-package"></a>Adicionar um pacote de aplicação

Utilize o portal do Azure para adicionar o ffmpeg à sua conta do Batch como um [pacote de aplicação](batch-application-packages.md). Os pacotes de aplicação ajudam a gerir aplicações de tarefas e a respetiva implementação em nós de computação do conjunto. 

1. No portal Azure, clique em **Mais**  >  **serviços Contas Lote** e clique no nome da sua conta Batch.
3. Clique **em Aplicações**  >  **Adicionar**.
4. Para **aplicação id** insira *ffmpeg* , e uma versão pacote de *4.3.1*. Selecione o ficheiro zip do ffmpeg que transferiu anteriormente e, em seguida, clique em **OK**. O pacote de aplicação do ffmpeg é adicionado à sua conta do Batch.

![Adicionar pacote de aplicação](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Transferir e executar o exemplo

### <a name="download-the-sample"></a>Transferir o exemplo

[Transfira ou clonar a aplicação de exemplo](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) a partir do GitHub. Para clonar o repositório de aplicações de exemplo com um cliente Git, utilize o seguinte comando:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Navegue para o diretório que contém o ficheiro de solução do Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Abra o ficheiro de solução no Visual Studio e atualize as cadeias de credenciais em `Program.cs` com os valores obtidos para as suas contas. Por exemplo:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Além disso, certifique-se de que a referência do pacote de aplicação do ffmpeg na solução corresponde ao ID e à versão do pacote do ffmpeg que carregou para a sua conta do Batch.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>Compilar e executar o projeto de exemplo

Crie e execute a aplicação no Visual Studio ou na linha de comandos com os comandos `dotnet build` e `dotnet run`. Depois de executar a aplicação, reveja o código para saber o que faz cada parte da aplicação. Por exemplo, no Visual Studio:

* Clique com o botão direito na solução no Solution Explorer e clique em **Build Solution**. 

* Confirme o restauro de quaisquer pacotes NuGet, se lhe for pedido. Se precisar de transferir pacotes em falta, certifique-se de que o [NuGet Package Manager](https://docs.nuget.org/consume/installing-nuget) está instalado.

Em seguida, execute-o. Quando executar a aplicação de exemplo, o resultado da consola é semelhante ao seguinte. Durante a execução, ocorre uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` enquanto os nós de computação do conjunto são iniciados. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Aceda à conta do Batch no portal do Azure para monitorizar o conjunto, os nós de computação, o trabalho e as tarefas. Por exemplo, para ver um mapa de calor dos nós de computação na sua piscina, clique em **Pools**  >  *WinFFmpegPool*.

Quando as tarefas estiverem em execução, o mapa térmico é semelhante ao seguinte:

![Mapa térmico do conjunto](./media/tutorial-parallel-dotnet/pool.png)

O tempo de execução normal é de aproximadamente **10 minutos** quando executa a aplicação na configuração predefinida. A criação do conjunto demora mais tempo.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Rever o código

As secções seguintes dividem a aplicação de exemplo nos passos que executa para processar uma carga de trabalho no serviço Batch. Veja o ficheiro `Program.cs` na solução enquanto lê o resto deste artigo, uma vez que nem todas as linhas de código no exemplo são abordadas.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticar clientes Blob e Batch

Para interagir com a conta de armazenamento associada, a aplicação utiliza a Biblioteca de Cliente de Armazenamento do Azure para .NET. Cria uma referência para a conta com [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) e autentica através da autenticação de chave partilhada. Em seguida, cria um [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

A aplicação cria um objeto [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) para criar e gerir conjuntos, trabalhos e tarefas no serviço Batch. O cliente Batch no exemplo utiliza a autenticação de chave partilhada. O Batch também suporta a autenticação através [do Azure Ative Directory](batch-aad-auth.md) para autenticar utilizadores individuais ou uma aplicação sem supervisão.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Carregar ficheiros de entrada

A aplicação transmite o objeto `blobClient` ao método `CreateContainerIfNotExistAsync` para criar um contentor de armazenamento para os ficheiros de entrada (formato MP4) e um contentor para o resultado da tarefa.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Em seguida, os ficheiros são carregados para o contentor de entrada a partir da pasta `InputFiles` local. Os ficheiros no armazenamento estão definidos como objetos [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) que o Batch pode transferir mais tarde para os nós de computação. 

Estão envolvidos dois métodos em `Program.cs` no carregamento de ficheiros:

* `UploadFilesToContainerAsync`: devolve uma coleção de objetos ResourceFile e chama internamente `UploadResourceFileToContainerAsync` para carregar cada ficheiro que é transmitido no parâmetro `inputFilePaths`.
* `UploadResourceFileToContainerAsync`: carrega cada ficheiro como um blob para o contentor de entrada. Depois de carregar o ficheiro, obtém uma assinatura de acesso partilhado (SAS) para o blob e devolve um objeto ResourceFile para a representar.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Para obter mais informações sobre como carregar ficheiros como blobs para uma conta de armazenamento com o .NET, veja [Carregar, transferir e listar blobs com o .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Em seguida, o exemplo cria um conjunto de nós de computação na conta do Batch, com uma chamada para `CreatePoolIfNotExistAsync`. Este método definido utiliza o método [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) para definir o número de nós, o tamanho da VM e uma configuração de conjuntos. Aqui, um objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) especifica uma [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) para uma imagem do Windows Server publicada no Azure Marketplace. O Batch suporta inúmeras imagens da VM no Azure Marketplace, bem como imagens da VM personalizadas.

O número de nós e o tamanho da VM são definidos através de constantes definidas. O lote suporta nós dedicados e [nós de baixa prioridade,](batch-low-pri-vms.md)e você pode usar qualquer um ou ambos nas suas piscinas. Os nós dedicados estão reservados para o conjunto. Os nós de prioridade baixa são disponibilizados a um preço reduzido a partir da capacidade excedente da VM no Azure. Os nós de prioridade baixa ficam indisponíveis se o Azure não tiver capacidade suficiente. Por predefinição, o exemplo cria um conjunto com apenas 5 nós de prioridade baixa no tamanho *Standard_A1_v2*.

>[!Note]
>Certifique-se de verificar as suas quotas de nó. Consulte [as quotas de serviço do Batch e os limites](batch-quota-limit.md#increase-a-quota) para instruções sobre como criar um pedido de quota."

A aplicação ffmpeg é implementada nos nós de computação ao adicionar [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) à configuração do conjunto.

O método [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) submete o conjunto ao serviço Batch.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Criar um trabalho

Um trabalho do Batch especifica um conjunto para executar tarefas e definições opcionais, como uma prioridade e agenda para o trabalho. O exemplo cria um trabalho com uma chamada para `CreateJobAsync`. Este método definido utiliza o método [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) para criar um trabalho no seu conjunto.

O método [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync) submete o conjunto ao serviço Batch. Inicialmente, o trabalho não tem tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Criar tarefas

O exemplo cria tarefas no trabalho com uma chamada para o método `AddTasksAsync`, que cria uma lista de objetos [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Cada `CloudTask` executa o ffmpeg para processar um objeto de entrada `ResourceFile` através de uma propriedade [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). O ffmpeg foi instalado anteriormente em cada nó quando o conjunto foi criado. Aqui, a linha de comandos executa o ffmpeg para converter cada ficheiro MP4 (vídeo) de entrada num ficheiro MP3 (áudio).

O exemplo cria um objeto [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) para o ficheiro MP3 depois de executar a linha de comandos. Os ficheiros de saída de cada tarefa (um, neste caso) são carregados para um contentor na conta de armazenamento associada através da propriedade [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) da tarefa. Anteriormente na amostra de código, foi obtido um URL de assinatura de acesso partilhado `outputContainerSasUrl` para fornecer acesso por escrito ao recipiente de saída. Note as condições definidas no `outputFile` objeto. Um ficheiro de saída de uma tarefa só é enviado para o recipiente depois de a tarefa ter sido concluída com sucesso ( `OutputFileUploadCondition.TaskSuccess` ). Consulte a amostra de [código](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) completa no GitHub para mais detalhes de implementação.

Em seguida, o exemplo adiciona tarefas ao trabalho com o método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync), o que as coloca em fila para serem executadas nos nós de computação.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Monitorizar tarefas

Quando o Batch adiciona tarefas a um trabalho, o serviço coloca automaticamente em fila e agenda-as para execução nos nós de computação no conjunto associado. Com base nas definições especificadas, o Batch processa todas as tarefas de colocação em fila, agendamento, repetições e outras funções de administração de tarefas.

Existem várias abordagens à monitorização da execução de tarefas. Este exemplo define um método `MonitorTasks` para comunicar apenas os estados de conclusão, falha ou êxito das tarefas. O código `MonitorTasks` especifica um [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) para selecionar eficientemente apenas informações mínimas sobre as tarefas. Em seguida, cria um [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor), que fornece utilitários de ajuda para monitorizar os estados das tarefas. Em `MonitorTasks`, o exemplo aguarda que todas as tarefas atinjam `TaskState.Completed` dentro de um limite de tempo. Em seguida, termina o trabalho e comunica todas as tarefas concluídas, mas que podem ter tido uma falha, como um código de saída diferente de zero.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar as tarefas, a aplicação elimina automaticamente o contentor de armazenamento de entrada que criou e dá-lhe a opção de eliminar o conjunto e o trabalho do Batch. As classes [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) e [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) do BatchClient têm métodos de eliminação correspondentes, que são chamados se confirmar a eliminação. Apesar de os próprios trabalhos e tarefas não lhe serem cobrados, os nós de computação são cobrados. Assim, recomendamos que atribua conjuntos apenas conforme necessário. Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados. No entanto, os ficheiros de saída permanecem na conta de armazenamento.

Quando já não forem necessários, elimine o grupo de recursos, a conta do Batch e a conta de armazenamento. Para tal, no portal do Azure, selecione o grupo de recursos da conta do Batch e clique em **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar um pacote de aplicação à sua conta do Batch
> * Autenticar com contas do Batch e de Armazenamento
> * Carregar ficheiros de entrada para o Armazenamento
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Criar um trabalho e tarefas para processar os ficheiros de entrada
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

Para obter mais exemplos de utilização da API .NET para agendar e processar cargas de trabalho do Batch, veja os exemplos no GitHub.

> [!div class="nextstepaction"]
> [Exemplos de C# para o Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)


Definir a variável de instância LowPriorityNodeCount=0 e o DedicadoNodeCount=5 corrigiu o problema e permitiu que o trabalho terminasse.
