---
title: Manter a saída de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros para .NET - Azure Batch | Documentos da Microsoft
description: Saiba como utilizar a biblioteca convenções de ficheiros do Azure Batch para .NET para manter a saída de tarefa e a tarefa de lote para o armazenamento do Azure e ver a saída persistente no portal do Azure.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69a3032ee96accdbafb32c96f4e9f3c89a0b3458
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539501"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Manter os dados de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros do Batch para .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma forma de manter os dados de tarefa é utilizar o [biblioteca de convenções de ficheiros do Azure Batch para .NET][nuget_package]. A biblioteca convenções de ficheiros simplifica o processo de armazenamento de dados de saída de tarefa ao armazenamento do Azure e recuperá-lo. Pode utilizar a biblioteca convenções de ficheiros no código de tarefas e de cliente &mdash; no código de tarefas para ficheiros persistentes e no código de cliente para listar e recuperá-los. Seu código de tarefas também pode utilizar a biblioteca para obter o resultado das tarefas a montante, como num [dependências de tarefas](batch-task-dependencies.md) cenário.

Para obter os ficheiros de saída com a biblioteca convenções de ficheiros, pode localizar os ficheiros para uma determinada tarefa ou a tarefa listando-os por ID e a finalidade. Não precisa de saber os nomes ou localizações dos ficheiros. Por exemplo, pode utilizar a biblioteca convenções de ficheiros para listar todos os arquivos intermediários para uma determinada tarefa ou obter um arquivo de pré-visualização para uma determinada tarefa.

> [!TIP]
> A partir da versão 2017-05-01, a API do serviço Batch suporta manter dados de saída para o armazenamento do Azure para tarefas e tarefas do Gestor de trabalho que são executadas em conjuntos criados com a configuração de máquina virtual. A API de serviço do Batch fornece uma forma simples de manter a saída de dentro do código que cria uma tarefa e serve como uma alternativa para a biblioteca convenções de ficheiros. Pode modificar as aplicações de cliente do Batch para manter a saída sem necessidade de atualizar a aplicação que a tarefa está em execução. Para obter mais informações, consulte [API do serviço de dados de tarefa de persistência para o armazenamento do Azure com o Batch](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando utiliza a biblioteca convenções de ficheiros para manter a saída de tarefa?

O Azure Batch fornece mais de uma forma de manter a saída de tarefa. As convenções de ficheiros é mais adequada a esses cenários:

- Pode modificar o código facilmente para a aplicação que a tarefa está em execução para manter os ficheiros com a biblioteca convenções de ficheiros.
- Pretende transmitir dados para o armazenamento do Azure enquanto a tarefa ainda está em execução.
- Pretende manter os dados de conjuntos criados com a configuração do serviço cloud ou a configuração de máquina virtual.
- A aplicação cliente ou outras tarefas na tarefa tem de localizar e transferir ficheiros de saída de tarefas por ID ou por finalidade.
- Pretende ver o resultado da tarefa no portal do Azure.

Se o seu cenário é diferente das listados acima, terá de considerar uma abordagem diferente. Para obter mais informações sobre outras opções para manter o resultado da tarefa, consulte [manter a saída de trabalhos e tarefas para o armazenamento do Azure](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>O que é o padrão de convenções de ficheiros Batch?

O [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) fornece um esquema de nomeação para os contentores de destino e caminhos dos BLOBs a que os ficheiros de saída são escritos. Ficheiros mantidas no armazenamento do Azure que aderem ao padrão de convenções de ficheiros estão automaticamente disponíveis para visualização no portal do Azure. O portal está atento a Convenção de nomenclatura e por isso, pode visualizar ficheiros que aderem ao mesmo.

A biblioteca convenções de ficheiros para .NET automaticamente nomes a contentores de armazenamento e ficheiros de saída da tarefa, de acordo com as convenções de ficheiros padrão. A biblioteca convenções de ficheiros também fornece métodos para ficheiros de saída no armazenamento do Azure, de acordo com o ID de tarefa, ID de tarefa ou para fins de consulta.

Se estiver a desenvolver com um idioma diferente do .NET, pode implementar o padrão de convenções de ficheiros-se na sua aplicação. Para obter mais informações, consulte [implementam o padrão de convenções de ficheiros do Batch](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Ligar uma conta de armazenamento do Azure à sua conta do Batch

Para manter os dados de saída para o armazenamento do Azure com a biblioteca convenções de ficheiros, primeiro tem de associar uma conta de armazenamento do Azure a sua conta do Batch. Se ainda não fez isso, ligar uma conta de armazenamento à sua conta do Batch ao utilizar o [portal do Azure](https://portal.azure.com):

1. No portal do Azure, navegue para a sua conta do Batch.
1. Sob **configurações**, selecione **conta de armazenamento**.
1. Se ainda não tiver uma conta de armazenamento associada a sua conta do Batch, clique em **conta de armazenamento (nenhum)**.
1. Selecione uma conta de armazenamento na lista para a sua subscrição. Para um melhor desempenho, utilize uma conta de armazenamento do Azure que está na mesma região que a conta do Batch em que estiver a executar suas tarefas.

## <a name="persist-output-data"></a>Manter os dados de saída

Para manter os dados de saída de trabalhos e tarefas com a biblioteca convenções de ficheiros, criar um contentor no armazenamento do Azure, em seguida, guarde a saída para o contentor. Utilize o [biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage) no código da tarefa para carregar o resultado da tarefa para o contentor. 

Para obter mais informações sobre como trabalhar com contentores e blobs no armazenamento do Azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Todos os resultados de trabalhos e tarefas persistentes com as convenções de ficheiro de biblioteca são armazenadas no mesmo contentor. Se tentar manter os ficheiros ao mesmo tempo, um grande número de tarefas [limites de conjunto de armazenamento](../storage/common/storage-performance-checklist.md#blobs) poderá ser forçado.

### <a name="create-storage-container"></a>Criar contentor de armazenamento

Para manter a saída de tarefa ao armazenamento do Azure, criar um contentor ao chamar [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Este método de extensão usa um [CloudStorageAccount] [ net_cloudstorageaccount] objeto como um parâmetro. Ele cria um contentor com o nome, de acordo com o padrão de convenções de ficheiros, para que seu conteúdo é detetável pelo portal do Azure e os métodos de recuperação discutidos posteriormente neste artigo.

Normalmente, colocar o código para criar um contentor na sua aplicação de cliente &mdash; do aplicativo que cria os conjuntos, trabalhos e tarefas.

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

### <a name="store-task-outputs"></a>Store saídas de tarefas

Agora que já preparou um contentor no armazenamento do Azure, tarefas podem economizar saída para o contentor utilizando o [TaskOutputStorage] [ net_taskoutputstorage] classe encontrada na biblioteca convenções de ficheiros.

Em seu código de tarefas, primeiro crie uma [TaskOutputStorage] [ net_taskoutputstorage] objeto, em seguida, quando a tarefa ter concluído o trabalho, chame o [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] método para salvar sua saída para o armazenamento do Azure.

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

O `kind` parâmetro do [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) método categoriza os ficheiros persistentes. Há quatro predefinidas [TaskOutputKind] [ net_taskoutputkind] tipos: `TaskOutput`, `TaskPreview`, `TaskLog`, e `TaskIntermediate.` também pode definir categorias personalizadas de saída.

Esses tipos de saída permitem-lhe especificar o tipo de saídas para listar quando consultar mais tarde o Batch para as saídas persistentes de uma determinada tarefa. Em outras palavras, ao listar as saídas para uma tarefa, pode filtrar a lista em um dos tipos de saída. Por exemplo, "dê-me os *pré-visualização* de saída para a tarefa *109*." Obter mais informações sobre a listagem e saídas de obter aparece na saída de obter posteriormente neste artigo.

> [!TIP]
> O tipo de saída também determina onde no portal do Azure um determinado arquivo aparece: *TaskOutput*-categorizados arquivos são apresentados em **ficheiros de saída da tarefa**, e *TaskLog* ficheiros são apresentados em **registos de tarefas**.

### <a name="store-job-outputs"></a>Store saídas de tarefas

Além de armazenar as saídas de tarefas, pode armazenar as saídas associadas a um trabalho inteiro. Por exemplo, a tarefa de intercalação de uma tarefa de composição do filme, pode manter o filme totalmente renderizado como uma saída de tarefa. Quando a tarefa estiver concluída, a aplicação cliente pode listar e obter os resultados para a tarefa e não necessita de consultar as tarefas individuais.

Store o resultado da tarefa ao chamar o [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] método e especifique o [JobOutputKind] [ net_joboutputkind] e nome de ficheiro:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Tal como acontece com o **TaskOutputKind** tipo para saídas de tarefas, utilize o [JobOutputKind] [ net_joboutputkind] tipo para categorizar uma tarefa do persistente a ficheiros. Este parâmetro permite consultar posterior para um tipo específico de saída (lista). O **JobOutputKind** tipo inclui as categorias de saída e pré-visualização e oferece suporte à criação de categorias personalizadas.

### <a name="store-task-logs"></a>Registos de tarefas de Store

Para além de persistência de um ficheiro para o armazenamento durável quando uma tarefa ou a tarefa estiver concluída, poderá ter de manter os ficheiros que são atualizados durante a execução de uma tarefa &mdash; ficheiros de registo ou `stdout.txt` e `stderr.txt`, por exemplo. Para esse fim, a biblioteca convenções de ficheiros do Azure Batch fornece a [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] método. Com o [SaveTrackedAsync][net_savetrackedasync], pode controlar atualizações para um ficheiro no nó (num intervalo que especificou) e manter essas atualizações ao armazenamento do Azure.

No trecho de código a seguir, usamos [SaveTrackedAsync] [ net_savetrackedasync] para atualizar `stdout.txt` no armazenamento do Azure durante a execução da tarefa a cada 15 segundos:

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

A secção Comentada `Code to process data and produce output file(s)` é um marcador de posição para o código que normalmente seria executar a sua tarefa. Por exemplo, pode ter um código que transfere dados a partir do armazenamento do Azure e realiza a transformação ou de cálculo no mesmo. A parte importante desse trecho de código é demonstrar como pode encapsular esse código num `using` bloco para atualizar periodicamente um ficheiro com [SaveTrackedAsync][net_savetrackedasync].

O agente de nó é um programa que é executado em cada nó no conjunto e fornece a interface de comando e controlo entre o nó e o serviço Batch. O `Task.Delay` chamada é necessária no final desta `using` bloco para garantir que o agente de nó tem tempo para liberar o conteúdo do padrão para o ficheiro de stdout.txt no nó. Sem este atraso, é possível perceber os último alguns segundos de saída. Este atraso pode não ser necessário para todos os ficheiros.

> [!NOTE]
> Quando ativa o ficheiro de controlo com **SaveTrackedAsync**, apenas *acrescenta* para o ficheiro controlado serem mantidas no armazenamento do Azure. Utilize este método apenas para ficheiros de registo não girando ou outros ficheiros que são escritos com operações de acréscimo ao fim do ficheiro de controlo.

## <a name="retrieve-output-data"></a>Obter dados de saída

Quando recupera a saída persistente usando a biblioteca convenções de ficheiros do Azure Batch, fazer isso de uma forma de tarefa e tarefa centradas no. Pode pedir a saída para determinados tarefas sem terem de conhecer seu caminho no armazenamento do Azure, ou até mesmo seu nome de ficheiro. Em vez disso, pode pedir ficheiros de saída por tarefas ou ID da tarefa

O fragmento de código seguinte itera através de tarefas de um trabalho, imprime algumas informações sobre os ficheiros de saída para a tarefa e, em seguida, transfere os ficheiros do armazenamento.

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

## <a name="view-output-files-in-the-azure-portal"></a>Ver ficheiros de saída no portal do Azure

O portal do Azure apresenta os ficheiros de saída de tarefa e os registos que persistem para um armazenamento do Azure ligada conta utilizando a [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Pode implementar essas convenções na uma linguagem de sua escolha, ou pode utilizar a biblioteca convenções de ficheiros em seus aplicativos .NET.

Para ativar a exibição dos seus ficheiros de saída no portal, tem de cumprir os seguintes requisitos:

1. Ligar uma conta de armazenamento do Azure à sua conta do Batch.
1. Seguir as convenções de nomenclatura predefinidas para ficheiros e contentores de armazenamento ao persistir saídas. Pode encontrar a definição dessas convenções na biblioteca convenções de ficheiros [Leia-me][github_file_conventions_readme]. Se utilizar o [convenções de ficheiros do Azure Batch] [ nuget_package] biblioteca para manter a sua saída, os ficheiros são mantidos, de acordo com o padrão de convenções de ficheiros.

Para ver os registos e ficheiros de saída da tarefa no portal do Azure, navegue para a tarefa cuja saída que está interessado, em seguida, clique em **ficheiros de saída guardados** ou **guardado registos**. Esta imagem mostra o **ficheiros de saída guardados** para a tarefa com o ID "007":

![Painel de saídas de tarefas no portal do Azure][2]

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs] [ github_persistoutputs] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra como utilizar a biblioteca convenções de ficheiros do Azure Batch para manter a saída de tarefas para o armazenamento durável. Para executar o exemplo, siga estes passos:

1. Abra o projeto na **Visual Studio 2017**.
2. Adicionar o Batch e de armazenamento **credenciais da conta** ao **AccountSettings.settings** no projeto Samples.
3. **Criar** (mas não são executados) a solução. Restaure quaisquer pacotes de NuGet se lhe for pedido.
4. Utilizar o portal do Azure para carregar um [pacote de aplicação](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e respetivas assemblagens dependentes no pacote. zip, defina o ID da aplicação para "PersistOutputsTask" e a versão do pacote de aplicação para "1.0".
5. **Inicie** (executar) o **PersistOutputs** projeto.
6. Quando lhe for pedido para escolher a tecnologia de persistência para utilizar para a execução do exemplo, introduza **1** para executar o exemplo usando a biblioteca convenções de ficheiros para manter a saída de tarefa. 

## <a name="next-steps"></a>Passos Seguintes

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obter a biblioteca convenções de ficheiros do Batch para .NET

A biblioteca convenções de ficheiros do Batch para .NET está disponível no [NuGet][nuget_package]. A biblioteca amplia o [CloudJob] [ net_cloudjob] e [CloudTask] [ net_cloudtask] classes com novos métodos. Consulte também os [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca convenções de ficheiros.

O [código-fonte] [ github_file_conventions] para as convenções de ficheiro de biblioteca está disponível no GitHub no Microsoft Azure SDK para o repositório .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explore outras abordagens para manter os dados de saída

- Ver [manter a saída de trabalhos e tarefas para o armazenamento do Azure](batch-task-output.md) para uma descrição geral de manter dados de tarefa e a tarefa.
- Ver [API do serviço de dados de tarefa de persistência para o armazenamento do Azure com o Batch](batch-task-output-files.md) para saber como utilizar a API de serviço do Batch para manter os dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
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

[1]: ./media/batch-task-output/task-output-01.png "Ficheiros de saída guardados e seletores de registos guardado no portal"
[2]: ./media/batch-task-output/task-output-02.png "Painel de saídas de tarefas no portal do Azure"
