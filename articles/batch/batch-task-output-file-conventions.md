---
title: Manter a saída de trabalho e tarefa no armazenamento do Azure com a biblioteca de convenções de arquivo para .NET-Azure batch | Microsoft Docs
description: Saiba como usar a biblioteca de convenções de arquivo do lote do Azure para .NET para persistir a saída de trabalho e tarefa do lote para o armazenamento do Azure e exibir a saída persistente no portal do Azure.
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 306407252a6f5bc4071947b6aafa2d50bccc97a8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842738"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Manter os dados de trabalho e tarefa no armazenamento do Azure com a biblioteca de convenções de arquivo em lotes para .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma maneira de manter os dados da tarefa é usar a [biblioteca de convenções de arquivo do lote do Azure para .net][nuget_package]. A biblioteca de convenções de arquivo simplifica o processo de armazenar dados de saída de tarefa no armazenamento do Azure e recuperá-los. Você pode usar a biblioteca de convenções de arquivo no código de tarefa e de cliente &mdash; no código de tarefa para persistir arquivos e no código do cliente para listá-los e recuperá-los. O código de tarefa também pode usar a biblioteca para recuperar a saída de tarefas de upstream, como em um cenário de [dependências de tarefas](batch-task-dependencies.md) .

Para recuperar arquivos de saída com a biblioteca de convenções de arquivo, você pode localizar os arquivos de um determinado trabalho ou tarefa listando-os por ID e finalidade. Você não precisa saber os nomes ou locais dos arquivos. Por exemplo, você pode usar a biblioteca de convenções de arquivo para listar todos os arquivos intermediários de uma determinada tarefa ou obter um arquivo de visualização para um determinado trabalho.

> [!TIP]
> A partir da versão 2017-05-01, a API do serviço de lote dá suporte à persistência de dados de saída no armazenamento do Azure para tarefas e tarefas do Gerenciador de trabalho que são executadas em pools criados com a configuração de máquina virtual. A API do serviço de lote fornece uma maneira simples de manter a saída de dentro do código que cria uma tarefa e serve como uma alternativa à biblioteca de convenções de arquivo. Você pode modificar os aplicativos cliente do lote para manter a saída sem a necessidade de atualizar o aplicativo que a tarefa está executando. Para obter mais informações, consulte [manter dados da tarefa no armazenamento do Azure com a API do serviço de lote](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando usar a biblioteca de convenções de arquivo para manter a saída da tarefa?

O lote do Azure fornece mais de uma maneira de manter a saída da tarefa. As convenções de arquivo são mais adequadas para esses cenários:

- Você pode modificar facilmente o código para o aplicativo que sua tarefa está executando para manter os arquivos usando a biblioteca de convenções de arquivo.
- Você deseja transmitir dados para o armazenamento do Azure enquanto a tarefa ainda está em execução.
- Você deseja manter os dados dos pools criados com a configuração do serviço de nuvem ou com a configuração da máquina virtual.
- O aplicativo cliente ou outras tarefas no trabalho precisam localizar e baixar arquivos de saída de tarefa por ID ou por finalidade.
- Você deseja exibir a saída da tarefa no portal do Azure.

Se seu cenário for diferente daqueles listados acima, talvez seja necessário considerar uma abordagem diferente. Para obter mais informações sobre outras opções para manter a saída da tarefa, consulte [manter a saída do trabalho e da tarefa no armazenamento do Azure](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>O que é o padrão de convenções de arquivo em lotes?

O [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) fornece um esquema de nomenclatura para os contêineres de destino e os caminhos de BLOB para os quais os arquivos de saída são gravados. Os arquivos persistidos no armazenamento do Azure que aderem ao padrão de convenções de arquivo estão automaticamente disponíveis para exibição no portal do Azure. O portal está ciente da Convenção de nomenclatura e, portanto, pode exibir arquivos que aderem a ela.

A biblioteca de convenções de arquivo para .NET nomeia automaticamente seus contêineres de armazenamento e arquivos de saída de tarefa de acordo com o padrão de convenções de arquivo. A biblioteca de convenções de arquivo também fornece métodos para consultar arquivos de saída no armazenamento do Azure de acordo com a ID do trabalho, a ID da tarefa ou a finalidade.

Se você estiver desenvolvendo com um idioma diferente do .NET, poderá implementar o padrão de convenções de arquivo em seu aplicativo. Para obter mais informações, consulte [implementar o padrão de convenções de arquivo em lotes](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Vincular uma conta de armazenamento do Azure à sua conta do lote

Para manter os dados de saída no armazenamento do Azure usando a biblioteca de convenções de arquivo, primeiro você deve vincular uma conta de armazenamento do Azure à sua conta do lote. Se você ainda não fez isso, vincule uma conta de armazenamento à sua conta do lote usando o [portal do Azure](https://portal.azure.com):

1. No portal do Azure, navegue para a sua conta do Batch.
1. Em **configurações**, selecione **conta de armazenamento**.
1. Se você ainda não tiver uma conta de armazenamento associada à sua conta do lote, clique em **conta de armazenamento (nenhuma)** .
1. Selecione uma conta de armazenamento na lista para sua assinatura. Para obter o melhor desempenho, use uma conta de armazenamento do Azure que esteja na mesma região que a conta do lote em que as tarefas estão em execução.

## <a name="persist-output-data"></a>Manter dados de saída

Para manter os dados de saída de trabalho e tarefa com a biblioteca de convenções de arquivo, crie um contêiner no armazenamento do Azure e, em seguida, salve a saída no contêiner. Use a [biblioteca de cliente de armazenamento do Azure para .net](https://www.nuget.org/packages/WindowsAzure.Storage) em seu código de tarefa para carregar a saída da tarefa para o contêiner.

Para obter mais informações sobre como trabalhar com contêineres e blobs no armazenamento do Azure, consulte Introdução [ao armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Todas as saídas de trabalho e tarefa persistidas com a biblioteca de convenções de arquivo são armazenadas no mesmo contêiner. Se um grande número de tarefas tentar persistir arquivos ao mesmo tempo, os limites de limitação de armazenamento do Azure poderão ser aplicados. Para obter mais informações sobre limites de limitação, consulte [lista de verificação de desempenho e escalabilidade para armazenamento de BLOBs](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Criar contentor de armazenamento

Para manter a saída da tarefa no armazenamento do Azure, primeiro crie um contêiner chamando [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Esse método de extensão usa um objeto [CloudStorageAccount][net_cloudstorageaccount] como um parâmetro. Ele cria um contêiner chamado de acordo com o padrão de convenções de arquivo, para que seu conteúdo seja detectável pelo portal do Azure e pelos métodos de recuperação discutidos posteriormente neste artigo.

Normalmente, você coloca o código para criar um contêiner em seu aplicativo cliente &mdash; o aplicativo que cria seus pools, trabalhos e tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Armazenar saídas de tarefa

Agora que você preparou um contêiner no armazenamento do Azure, as tarefas podem salvar a saída no contêiner usando a classe [TaskOutputStorage][net_taskoutputstorage] encontrada na biblioteca de convenções de arquivo.

Em seu código de tarefa, primeiro crie um objeto [TaskOutputStorage][net_taskoutputstorage] e, em seguida, quando a tarefa tiver concluído seu trabalho, chame o [TaskOutputStorage][net_taskoutputstorage]. Método [SaveAsync][net_saveasync] para salvar sua saída no armazenamento do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O parâmetro `kind` do [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). O método [SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) categoriza os arquivos persistentes. Há quatro tipos de [TaskOutputKind][net_taskoutputkind] predefinidos: `TaskOutput`, `TaskPreview`, `TaskLog`e `TaskIntermediate.` você também pode definir categorias personalizadas de saída.

Esses tipos de saída permitem que você especifique o tipo de saídas a serem listadas quando posteriormente você consulta o lote para as saídas persistentes de uma determinada tarefa. Em outras palavras, ao listar as saídas de uma tarefa, você pode filtrar a lista em um dos tipos de saída. Por exemplo, "Dê-me a saída de *Visualização* para a tarefa *109*". Mais informações sobre a listagem e a recuperação de saídas são exibidas em recuperar saída posteriormente neste artigo.

> [!TIP]
> O tipo de saída também determina onde no portal do Azure um arquivo específico é exibido: os arquivos categorizados *TaskOutput*aparecem **em arquivos de saída da tarefa**e os arquivos *tasklog* aparecem em logs de **tarefas**.

### <a name="store-job-outputs"></a>Armazenar saídas de trabalho

Além de armazenar saídas de tarefa, você pode armazenar as saídas associadas a um trabalho inteiro. Por exemplo, na tarefa de mesclagem de um trabalho de renderização de filme, você pode persistir o filme totalmente renderizado como uma saída de trabalho. Quando o trabalho for concluído, o aplicativo cliente poderá listar e recuperar as saídas do trabalho e não precisará consultar as tarefas individuais.

Armazene a saída do trabalho chamando o [JobOutputStorage][net_joboutputstorage]. Método [SaveAsync][net_joboutputstorage_saveasync] e especifique o [JobOutputKind][net_joboutputkind] e o nome de arquivo:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Assim como com o tipo **TaskOutputKind** para saídas de tarefa, você usa o tipo [JobOutputKind][net_joboutputkind] para categorizar os arquivos persistentes de um trabalho. Esse parâmetro permite que você consulte posteriormente (List) um tipo específico de saída. O tipo **JobOutputKind** inclui as categorias output e Preview e dá suporte à criação de categorias personalizadas.

### <a name="store-task-logs"></a>Armazenar logs de tarefas

Além de persistir um arquivo para o armazenamento durável quando uma tarefa ou trabalho é concluído, talvez seja necessário manter os arquivos que são atualizados durante a execução de uma tarefa &mdash; arquivos de log ou `stdout.txt` e `stderr.txt`, por exemplo. Para essa finalidade, a biblioteca de convenções de arquivo do lote do Azure fornece o [TaskOutputStorage][net_taskoutputstorage]. Método [SaveTrackedAsync][net_savetrackedasync] . Com o [SaveTrackedAsync][net_savetrackedasync], você pode controlar as atualizações em um arquivo no nó (em um intervalo que você especificar) e manter essas atualizações no armazenamento do Azure.

No trecho de código a seguir, usamos [SaveTrackedAsync][net_savetrackedasync] para atualizar `stdout.txt` no armazenamento do Azure a cada 15 segundos durante a execução da tarefa:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

A seção comentada `Code to process data and produce output file(s)` é um espaço reservado para o código que sua tarefa normalmente executaria. Por exemplo, você pode ter um código que baixa dados do armazenamento do Azure e executa a transformação ou o cálculo nele. A parte importante desse trecho de código é demonstrar como você pode encapsular esse tipo em um bloco de `using` para atualizar periodicamente um arquivo com [SaveTrackedAsync][net_savetrackedasync].

O agente de nó é um programa executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote. A chamada de `Task.Delay` é necessária no final deste bloco de `using` para garantir que o agente de nó tenha tempo para liberar o conteúdo padrão para o arquivo stdout. txt no nó. Sem esse atraso, é possível perder os últimos segundos de saída. Esse atraso pode não ser necessário para todos os arquivos.

> [!NOTE]
> Quando você habilita o rastreamento de arquivos com o **SaveTrackedAsync**, apenas *anexações* ao arquivo rastreado são persistidas no armazenamento do Azure. Use esse método somente para controlar arquivos de log sem rotação ou outros arquivos que são gravados em operações de acréscimo no final do arquivo.

## <a name="retrieve-output-data"></a>Recuperar dados de saída

Quando você recupera a saída persistente usando a biblioteca de convenções de arquivo do lote do Azure, você faz isso de maneira centrada em tarefa e trabalho. Você pode solicitar a saída para determinada tarefa ou trabalho sem precisar saber seu caminho no armazenamento do Azure ou até mesmo seu nome de arquivo. Em vez disso, você pode solicitar arquivos de saída por ID de tarefa ou trabalho.

O trecho de código a seguir itera as tarefas de um trabalho, imprime algumas informações sobre os arquivos de saída para a tarefa e, em seguida, baixa seus arquivos do armazenamento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Exibir arquivos de saída no portal do Azure

O portal do Azure exibe os arquivos de saída de tarefa e os logs que são persistidos em uma conta de armazenamento do Azure vinculada usando o [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Você mesmo pode implementar essas convenções na linguagem de sua escolha ou pode usar a biblioteca de convenções de arquivo em seus aplicativos .NET.

Para habilitar a exibição dos arquivos de saída no portal, você deve atender aos seguintes requisitos:

1. Vincule uma conta de armazenamento do Azure à sua conta do lote.
1. Siga as convenções de nomenclatura predefinidas para armazenar arquivos e contêineres ao persistir saídas. Você pode encontrar a definição dessas convenções na biblioteca de convenções de arquivo [Leiame][github_file_conventions_readme]. Se você usar a biblioteca de [convenções de arquivo do lote do Azure][nuget_package] para persistir sua saída, seus arquivos serão persistidos de acordo com o padrão de convenções de arquivo.

Para exibir arquivos de saída de tarefa e logs no portal do Azure, navegue até a tarefa cuja saída você está interessado e clique em **arquivos de saída salvos** ou **logs salvos**. Esta imagem mostra os **arquivos de saída salvos** para a tarefa com a ID "007":

![Folha de saídas de tarefa no portal do Azure][2]

## <a name="code-sample"></a>Exemplo de código

O projeto de exemplo [PersistOutputs][github_persistoutputs] é um dos [exemplos de código do lote do Azure][github_samples] no github. Esta solução do Visual Studio demonstra como usar a biblioteca de convenções de arquivo do lote do Azure para manter a saída da tarefa para o armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2019**.
2. Adicione suas **credenciais de conta** de armazenamento e lote a **AccountSettings. Settings** no projeto Microsoft. Azure. Batch. Samples. Common.
3. **Compile** (mas não execute) a solução. Restaure todos os pacotes NuGet, se solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Inclua o `PersistOutputsTask.exe` e seus assemblies dependentes no pacote. zip, defina a ID do aplicativo como "PersistOutputsTask" e a versão do pacote de aplicativos como "1,0".
5. **Inicie** (Execute) o projeto **PersistOutputs** .
6. Quando for solicitado a escolher a tecnologia de persistência a ser usada para executar o exemplo, insira **1** para executar o exemplo usando a biblioteca de convenções de arquivo para manter a saída da tarefa. 

## <a name="next-steps"></a>Passos seguintes

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obter a biblioteca de convenções de arquivo em lotes para .NET

A biblioteca de convenções de arquivo em lotes para .NET está disponível no [NuGet][nuget_package]. A biblioteca estende as classes [CloudJob][net_cloudjob] e [CloudTask][net_cloudtask] com novos métodos. Consulte também a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca de convenções de arquivo.

O [código-fonte][github_file_conventions] para a biblioteca de convenções de arquivo está disponível no GitHub no repositório Microsoft Azure SDK para .net. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explore outras abordagens para manter os dados de saída

- Consulte [manter a saída do trabalho e da tarefa no armazenamento do Azure](batch-task-output.md) para obter uma visão geral de persistir dados de tarefa e de trabalho.
- Consulte [manter dados da tarefa no armazenamento do Azure com a API do serviço de lote](batch-task-output-files.md) para saber como usar a API do serviço de lote para manter os dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Arquivos de saída salvos e seletores de logs salvos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Folha de saídas de tarefa no portal do Azure"
