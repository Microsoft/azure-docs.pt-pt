---
title: Persistir dados de saída para o Armazenamento Azure com a biblioteca .NET File Conventions - Azure Batch
description: Aprenda a utilizar a biblioteca de Convenções de Ficheiros de Lote Azure para .NET persistir a tarefa de Lote & saída de trabalho para o Armazenamento Azure, e ver essa saída no portal Azure.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e331dcfc9acf7a5e8a83be788b566cf92eaeb8f4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548051"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Persistir dados de trabalho e tarefa sintetizar para o Armazenamento Azure com a biblioteca de Convenções de Ficheiros de Lote para .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma forma de persistir os dados de tarefas é utilizar a biblioteca de Convenções de [Ficheiros de Lote De Lote Azure para .NET][nuget_package]. A biblioteca de Convenções de Ficheiros simplifica o processo de armazenamento de dados de saída de tarefas para o Armazenamento de Azure e recupera-os. Pode utilizar a biblioteca de Convenções de &mdash; Ficheiros no código de tarefa e do cliente no código de tarefa para ficheiros persistentes, e no código do cliente para listar e recuperá-los. O seu código de tarefa também pode usar a biblioteca para recuperar a saída de tarefas a montante, como num cenário de [dependênciade tarefas.](batch-task-dependencies.md)

Para recuperar ficheiros de saída com a biblioteca de Convenções de Ficheiros, pode localizar os ficheiros para um determinado trabalho ou tarefa, listando-os por ID e propósito. Não precisas de saber os nomes ou localizações dos ficheiros. Por exemplo, pode utilizar a biblioteca de Convenções de Ficheiros para listar todos os ficheiros intermédios para uma determinada tarefa, ou obter um ficheiro de pré-visualização para um determinado trabalho.

> [!TIP]
> Começando pela versão 2017-05-01, o serviço Delote API suporta dados de saída persistentes para o Armazenamento Azure para tarefas e tarefas de gestor de emprego que funcionam em piscinas criadas com a configuração da máquina virtual. O serviço de lote API fornece uma forma simples de persistir a saída a partir do código que cria uma tarefa e serve como uma alternativa à biblioteca de Convenções de Ficheiros. Pode modificar as suas aplicações de cliente do Batch para persistir a saída sem precisar de atualizar a aplicação que a sua tarefa está a executar. Para mais informações, consulte os dados de [tarefa sinuosos para o Armazenamento Azure com o serviço de lote API](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando uso a biblioteca de Convenções de Arquivo para persistir na saída de tarefas?

O Lote Azure fornece mais de uma forma de persistir a saída de tarefas. As Convenções de Arquivo são mais adequadas a estes cenários:

- Pode modificar facilmente o código da aplicação que a sua tarefa está a executar para persistir ficheiros utilizando a biblioteca de Convenções de Ficheiros.
- Pretende transmitir dados para o Armazenamento Azure enquanto a tarefa ainda está em execução.
- Pretende persistir dados de piscinas criadas com a configuração do serviço de nuvem ou a configuração da máquina virtual.
- A sua aplicação de cliente ou outras tarefas no trabalho precisa de localizar e descarregar ficheiros de saída de tarefas por ID ou por propósito.
- Pretende ver a saída de tarefas no portal Azure.

Se o seu cenário difere dos acima listados, poderá ter de considerar uma abordagem diferente. Para obter mais informações sobre outras opções para a persistência da saída de tarefas, consulte [a saída de trabalho e tarefa sinuosa para o Armazenamento Azure](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>O que é a norma das Convenções de Ficheiros de Lote?

A [norma De convenções](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) de ficheiros de lote fornece um esquema de nomeação para os contentores de destino e caminhos blob para os quais os seus ficheiros de saída estão escritos. Os ficheiros persistiam no Armazenamento Azure que aderir à norma de Convenções de Ficheiros estão automaticamente disponíveis para visualização no portal Azure. O portal está ciente da convenção de nomeação e assim pode exibir ficheiros que lhe aderem.

A biblioteca de Convenções de Ficheiros para .NET designa automaticamente os seus contentores de armazenamento e ficheiros de saída de tarefas de acordo com a norma de Convenções de Ficheiros. A biblioteca de Convenções de Ficheiros também fornece métodos para consultar ficheiros de saída no Armazenamento Azure de acordo com o ID de trabalho, id de tarefa ou finalidade.

Se estiver a desenvolver-se com uma língua diferente de .NET, pode implementar o padrão de Convenções de Ficheiros na sua aplicação. Para mais informações, consulte [a norma 'Implementar as Convenções de Ficheiros de Lote'.](batch-task-output.md#implement-the-batch-file-conventions-standard)

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Ligue uma conta de Armazenamento Azure à sua conta Batch

Para persistir os dados de saída do Azure Storage utilizando a biblioteca de Convenções de Ficheiros, tem primeiro de ligar uma conta de Armazenamento Azure à sua conta De lote. Se ainda não o fez, ligue uma conta de Armazenamento à sua conta De lote utilizando o [portal Azure:](https://portal.azure.com)

1. No portal do Azure, navegue para a sua conta do Batch.
1. Em **Definições,** selecione **Conta de Armazenamento**.
1. Se ainda não tiver uma conta de Armazenamento associada à sua conta 'Lote', clique na **Conta de Armazenamento (Nenhuma)**.
1. Selecione uma conta de Armazenamento da lista para a sua subscrição. Para um melhor desempenho, utilize uma conta de Armazenamento Azure que se encontra na mesma região da conta Batch onde as suas tarefas estão em execução.

## <a name="persist-output-data"></a>Persistir dados de produção

Para persistir os dados de saída de trabalho e tarefa com a biblioteca de Convenções de Ficheiros, crie um recipiente no Armazenamento Azure e, em seguida, guarde a saída para o recipiente. Utilize a biblioteca do [cliente Azure Storage para .NET](https://www.nuget.org/packages/WindowsAzure.Storage) no seu código de tarefa para fazer o upload da saída de tarefa para o recipiente.

Para obter mais informações sobre o trabalho com contentores e bolhas no Armazenamento Azure, consulte [Start start with Azure Blob storage using .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Todas as saídas de trabalho e tarefas permaneis com a biblioteca de Convenções de Arquivo são armazenadas no mesmo recipiente. Se um grande número de tarefas tentar persistir ficheiros ao mesmo tempo, os limites de estrangulamento do Armazenamento Azure podem ser aplicados. Para obter mais informações sobre os limites de estrangulamento, consulte a lista de verificação de [desempenho e escalabilidade para](../storage/blobs/storage-performance-checklist.md)armazenamento blob .

### <a name="create-storage-container"></a>Criar contentor de armazenamento

Para persistir a saída de tarefas para o Armazenamento Azure, crie primeiro um recipiente chamando [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Este método de extensão toma um objeto [CloudStorageAccount][net_cloudstorageaccount] como parâmetro. Cria um contentor nomeado de acordo com a norma De acordo com a norma Desemposições de Arquivo, de modo a que os seus conteúdos sejam detetáveis pelo portal Azure e pelos métodos de recuperação discutidos posteriormente no artigo.

Normalmente, coloca o código para criar &mdash; um contentor na aplicação do seu cliente, a aplicação que cria as suas piscinas, empregos e tarefas.

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

### <a name="store-task-outputs"></a>Armazenar saídas de tarefas

Agora que preparou um contentor no Armazenamento Azure, as tarefas podem economizar saída para o contentor utilizando a classe [TaskOutputStorage][net_taskoutputstorage] encontrada na biblioteca de Convenções de Ficheiros.

No seu código de tarefa, crie primeiro um objeto [TaskOutputStorage,][net_taskoutputstorage] depois quando a tarefa tiver concluído o seu trabalho, ligue para o [TaskOutputStorage][net_taskoutputstorage]. [Método SaveAsync][net_saveasync] para salvar a sua saída para o Armazenamento Azure.

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

O `kind` parâmetro do [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). O método [SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) categoriza os ficheiros persistidos. Existem quatro tipos predefinidos `TaskOutput` `TaskPreview`de `TaskLog` [TaskOutputKind:][net_taskoutputkind] , , e `TaskIntermediate.` pode também definir categorias personalizadas de saída.

Estes tipos de saída permitem especificar que tipo de saídas listar quando consulta mais tarde o Batch para as saídas persistentes de uma determinada tarefa. Por outras palavras, quando lista as saídas para uma tarefa, pode filtrar a lista num dos tipos de saída. Por exemplo, "Dê-me a saída de *pré-visualização* para a tarefa *109*." Mais sobre a listagem e recuperação de saídas aparece na saída de Recuperar mais tarde no artigo.

> [!TIP]
> O tipo de saída também determina onde no portal Azure aparece um determinado ficheiro: Ficheiros categorizados por *TaskOutput*aparecem nos **ficheiros**de saída da Task , e os ficheiros *TaskLog* aparecem nos registos de **Tarefas**.

### <a name="store-job-outputs"></a>Armazenar saídas de trabalho

Além de armazenar saídas de tarefas, pode armazenar as saídas associadas a um trabalho inteiro. Por exemplo, na tarefa de fusão de um trabalho de renderização de filmes, você poderia persistir o filme totalmente renderizado como uma saída de trabalho. Quando o seu trabalho estiver concluído, a sua aplicação de cliente pode listar e recuperar as saídas para o trabalho, e não precisa de consultar as tarefas individuais.

Armazenar saída de trabalho chamando o [JobOutputStorage][net_joboutputstorage]. [Método SaveAsync][net_joboutputstorage_saveasync] e especificar o [JobOutputKind][net_joboutputkind] e o nome de ficheiro:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Tal como acontece com o tipo **TaskOutputKind** para saídas de tarefas, utiliza-se o tipo [JobOutputKind][net_joboutputkind] para categorizar os ficheiros persistidos de um trabalho. Este parâmetro permite-lhe consultar posteriormente (lista) um tipo específico de saída. O tipo **JobOutputKind** inclui categorias de saída e pré-visualização, e suporta a criação de categorias personalizadas.

### <a name="store-task-logs"></a>Armazenar registos de tarefas

Além de persistir um ficheiro para armazenamento duradouro quando uma tarefa ou trabalho estiver concluído, poderá &mdash; ter `stdout.txt` de `stderr.txt`persistir ficheiros que sejam atualizados durante a execução de ficheiros de registo de tarefas ou, por exemplo. Para o efeito, a biblioteca de Convenções de Ficheiros de Lote Azure fornece o [TaskOutputStorage][net_taskoutputstorage]. [Método SaveTrackedAsync.][net_savetrackedasync] Com o [SaveTrackedAsync,][net_savetrackedasync]pode rastrear atualizações para um ficheiro no nó (num intervalo que especifica) e persistir nessas atualizações para o Armazenamento Azure.

No seguinte código, utilizamos [saveTrackedAsync][net_savetrackedasync] para `stdout.txt` atualizar em Armazenamento Azure a cada 15 segundos durante a execução da tarefa:

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

A secção `Code to process data and produce output file(s)` comentada é um espaço reservado para o código que a sua tarefa normalmente executaria. Por exemplo, pode ter um código que descarrega dados do Armazenamento Azure e realiza transformação ou cálculo no mesmo. A parte importante deste corte é demonstrar como pode embrulhar `using` tal código num bloco para atualizar periodicamente um ficheiro com [SaveTrackedAsync][net_savetrackedasync].

O agente do nó é um programa que funciona em cada nó na piscina e fornece a interface de comando e controlo entre o nó e o serviço Batch. A `Task.Delay` chamada é necessária no `using` final deste bloco para garantir que o agente do nó tem tempo para descarregar o conteúdo da norma para o ficheiro stdout.txt no nó. Sem este atraso, é possível perder os últimos segundos de saída. Este atraso pode não ser necessário para todos os ficheiros.

> [!NOTE]
> Quando ativa o rastreio de ficheiros com **o SaveTrackedAsync,** apenas os *apêndices* do ficheiro rastreado são persistentes no Armazenamento Azure. Utilize este método apenas para rastrear ficheiros de registo não rotativos ou outros ficheiros que sejam escritos com operações de apêndice até ao fim do ficheiro.

## <a name="retrieve-output-data"></a>Recuperar dados de saída

Quando recuperar a sua saída persistente utilizando a biblioteca de Convenções de Ficheiros de Lote Azure, fá-lo de forma centrada na tarefa e no trabalho. Pode solicitar a saída para determinada tarefa ou trabalho sem precisar de conhecer o seu caminho no Armazenamento Azure, ou mesmo o seu nome de ficheiro. Em vez disso, pode solicitar ficheiros de saída por tarefa ou identificação de trabalho.

O código seguinte emite itépet através das tarefas de um trabalho, imprime algumas informações sobre os ficheiros de saída para a tarefa e, em seguida, descarrega os seus ficheiros a partir do Armazenamento.

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

## <a name="view-output-files-in-the-azure-portal"></a>Ver ficheiros de saída no portal Azure

O portal Azure exibe ficheiros de saída de tarefas e registos que são persistidos numa conta de armazenamento Azure ligada utilizando a [norma Deconvenções](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)de Ficheiros de Lote . Você pode implementar estas convenções você mesmo em uma linguagem à sua escolha, ou você pode usar a biblioteca de Convenções de Arquivo nas suas aplicações .NET.

Para ativar a visualização dos seus ficheiros de saída no portal, deve satisfazer os seguintes requisitos:

1. Ligue uma conta de Armazenamento Azure à sua conta Batch.
1. Aderir às convenções de nomeação predefinidas para recipientes e ficheiros de armazenamento quando persistir em saídas. Pode encontrar a definição destas convenções na biblioteca de Convenções de Arquivo [SLERT][github_file_conventions_readme]. Se utilizar a biblioteca de Convenções de [Ficheiros de Lote Azure][nuget_package] para persistir a sua saída, os seus ficheiros são persistentes de acordo com a norma de Convenções de Ficheiros.

Para visualizar ficheiros de saída de tarefas e registos no portal Azure, navegue para a tarefa cuja saída está interessada e, em seguida, clique em ficheiros de **saída Guardados** ou **em registos guardados**. Esta imagem mostra os ficheiros de **saída Guardados** para a tarefa com id "007":

![Lâmina de saída de tarefas no portal Azure][2]

## <a name="code-sample"></a>Exemplo de código

O projeto de amostra [PersistOutputs][github_persistoutputs] é uma das amostras de [código do Lote Azure][github_samples] no GitHub. Esta solução Visual Studio demonstra como usar a biblioteca de Convenções de Ficheiros de Lote Azure para persistir a saída de tarefas para armazenamento duradouro. Para executar a amostra, siga estes passos:

1. Abra o projeto no **Visual Studio 2019.**
2. Adicione **as suas credenciais** de conta de Lote e Armazenamento às **definições de Definições de Conta** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Construir** (mas não executar) a solução. Restaure quaisquer pacotes NuGet se solicitado.
4. Utilize o portal Azure para fazer upload de um pacote de [aplicações](batch-application-packages.md) para **persistOutputsTask**. Incluir `PersistOutputsTask.exe` os conjuntos e os seus conjuntos dependentes no pacote .zip, definir o ID da aplicação para "PersistOutputsTask", e a versão do pacote de aplicação para "1.0".
5. **Iniciar** (executar) o projeto **PersistOutputs.**
6. Quando solicitado a escolher a tecnologia de persistência a utilizar para executar a amostra, introduza **1** para executar a amostra utilizando a biblioteca de Convenções de Ficheiros para persistir na saída de tarefas. 

## <a name="next-steps"></a>Passos seguintes

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obtenha a biblioteca de Convenções de Ficheiros de Lote para .NET

A biblioteca de Convenções de Ficheiros de Lote para .NET está disponível no [NuGet][nuget_package]. A biblioteca estende as aulas [CloudJob][net_cloudjob] e [CloudTask][net_cloudtask] com novos métodos. Consulte também a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca de Convenções de Arquivo.

O [código fonte][github_file_conventions] da biblioteca de Convenções de Ficheiros está disponível no GitHub no Microsoft Azure SDK para o repositório .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explore outras abordagens para persistir dados de produção

- Consulte a saída de trabalho e tarefa da Persist para o [Armazenamento Azure](batch-task-output.md) para uma visão geral da persistência de dados de tarefas e de emprego.
- Consulte os dados de [tarefa sinuosos para o Armazenamento Azure com o serviço de lote API](batch-task-output-files.md) para aprender a usar o serviço de lote API para persistir os dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.cloudstorageaccount
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

[1]: ./media/batch-task-output/task-output-01.png "Ficheiros de saída guardados e selecionadores de registos guardados no portal"
[2]: ./media/batch-task-output/task-output-02.png "Lâmina de saída de tarefas no portal Azure"
