---
title: Persistir dados de saída para Azure Storage com biblioteca .NET File Conventions
description: Saiba como utilizar a biblioteca de convenções de ficheiros Azure Batch para que .NET persista a tarefa do Batch & saída de trabalho para o Azure Storage, e ver essa saída no portal Azure.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936967"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Persistir dados de trabalho e de tarefa para o Azure Storage com a biblioteca de convenções de ficheiros de lote para .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma forma de persistir os dados de tarefas é utilizar a [biblioteca de convenções de ficheiros Azure Batch para .NET][nuget_package]. A biblioteca de convenções de ficheiros simplifica o processo de armazenamento de dados de saída de tarefas para o Armazenamento de Azure e a sua recuperação. Pode utilizar a biblioteca de Convenções de Ficheiros em código de tarefa e cliente &mdash; em código de tarefa para ficheiros persistentes e no código do cliente para listá-los e recuperá-los. O seu código de tarefa também pode usar a biblioteca para recuperar a saída de tarefas a montante, como num cenário [de dependência de tarefas.](batch-task-dependencies.md)

Para recuperar ficheiros de saída com a biblioteca de Convenções de Ficheiros, pode localizar os ficheiros para uma determinada tarefa ou tarefa, listando-os por ID e finalidade. Não precisa de saber os nomes ou locais dos ficheiros. Por exemplo, pode utilizar a biblioteca de Convenções de Ficheiros para listar todos os ficheiros intermédios para uma determinada tarefa, ou obter um ficheiro de pré-visualização para um determinado trabalho.

> [!TIP]
> Começando pela versão 2017-05-01, a API de serviço batch suporta dados de saída persistentes para o Azure Storage para tarefas e tarefas de gestor de emprego que funcionam em piscinas criadas com a configuração da máquina virtual. A API de serviço de lote fornece uma forma simples de persistir a saída a partir do código que cria uma tarefa e serve como uma alternativa à biblioteca de Convenções de Ficheiros. Pode modificar as aplicações do seu cliente Batch para persistir na saída sem precisar de atualizar a aplicação que a sua tarefa está a executar. Para obter mais informações, consulte [os dados de tarefas persistindo para o Azure Storage com o serviço Batch API](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando é que uso a biblioteca de convenções de ficheiros para persistir na saída da tarefa?

O Azure Batch fornece mais de uma forma de persistir na saída da tarefa. As Convenções de Arquivo são as mais adequadas a estes cenários:

- Pode modificar facilmente o código para a aplicação que a sua tarefa está a executar para persistir ficheiros utilizando a biblioteca de Convenções de Ficheiros.
- Pretende transmitir dados para o Azure Storage enquanto a tarefa ainda estiver em execução.
- Pretende persistir dados de piscinas criadas com a configuração do serviço de nuvem ou com a configuração da máquina virtual.
- A sua aplicação ao cliente ou outras tarefas no trabalho precisa de localizar e transferir ficheiros de saída de tarefas por ID ou por finalidade.
- Pretende ver a saída da tarefa no portal Azure.

Se o seu cenário diferir dos listados acima, poderá ter de considerar uma abordagem diferente. Para obter mais informações sobre outras opções para a produção de tarefas persistentes, consulte [a saída de trabalho e tarefa persistente para o Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Qual é a norma das convenções de ficheiros de lote?

A [norma DeDecant file Conventions](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) fornece um esquema de nomeação para os contentores de destino e caminhos blob para os quais os seus ficheiros de saída estão escritos. Os ficheiros persistiram no Azure Storage que adere à norma de Convenções de Ficheiros estão automaticamente disponíveis para visualização no portal Azure. O portal está ciente da convenção de nomeação e assim pode exibir ficheiros que aderem a ele.

A biblioteca de Convenções de Ficheiros para .NET designa automaticamente os seus contentores de armazenamento e ficheiros de saída de tarefas de acordo com a norma das Convenções de Ficheiros. A biblioteca de convenções de ficheiros também fornece métodos para consultar ficheiros de saída no Azure Storage de acordo com o ID de trabalho, iD de tarefa ou finalidade.

Se estiver a desenvolver com um idioma diferente de .NET, pode implementar o padrão das Convenções de Ficheiros na sua aplicação. Para obter mais informações, consulte [implementar a norma de Convenções de Ficheiros de Lote](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Ligue uma conta de Armazenamento Azure à sua conta Batch

Para persistir os dados de saída para o Azure Storage utilizando a biblioteca de Convenções de Ficheiros, tem primeiro de ligar uma conta de Armazenamento Azure à sua conta Batch. Se ainda não o fez, ligue uma conta de Armazenamento à sua conta Batch utilizando o [portal Azure:](https://portal.azure.com)

1. No portal do Azure, navegue para a sua conta do Batch.
1. Em **Definições**, selecione **Conta de Armazenamento**.
1. Se ainda não tiver uma conta de Armazenamento associada à sua conta Batch, clique em **Conta de Armazenamento (Nenhuma)**.
1. Selecione uma conta de Armazenamento na lista para a sua subscrição. Para obter um melhor desempenho, utilize uma conta de Armazenamento Azure que esteja na mesma região que a conta Batch onde as suas tarefas estão a decorrer.

## <a name="persist-output-data"></a>Persistir dados de saída

Para persistir os dados de saída de trabalho e de tarefa com a biblioteca de Convenções de Ficheiros, crie um recipiente no Armazenamento Azure e guarde a saída para o recipiente. Utilize a [biblioteca cliente de Armazenamento Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage) no seu código de tarefa para carregar a saída da tarefa para o recipiente.

Para obter mais informações sobre o trabalho com recipientes e bolhas no Azure Storage, consulte [Começar com o armazenamento Azure Blob usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

> [!WARNING]
> Todas as saídas de trabalho e tarefas persistiu com a biblioteca de Convenções de Ficheiros são armazenadas no mesmo recipiente. Se um grande número de tarefas tentar persistir ficheiros ao mesmo tempo, os limites de estrangulamento do Azure Storage podem ser aplicados. Para obter mais informações sobre limites de estrangulamento, consulte [a lista de verificação de desempenho e escalabilidade para armazenamento blob](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Criar contentor de armazenamento

Para persistir a saída de tarefa para o Azure Storage, primeiro crie um recipiente chamando [CloudJob][net_cloudjob]. [Prepare oOutputStorageAsync][net_prepareoutputasync]. Este método de extensão toma um objeto [CloudStorageAccount][net_cloudstorageaccount] como um parâmetro. Cria um recipiente nomeado de acordo com a norma de Convenções de Arquivo, para que o seu conteúdo seja detetado pelo portal Azure e pelos métodos de recuperação discutidos mais tarde no artigo.

Normalmente coloca o código para criar um recipiente na aplicação do seu cliente &mdash; a aplicação que cria as suas piscinas, empregos e tarefas.

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

Agora que preparou um recipiente no Azure Storage, as tarefas podem economizar a saída para o contentor utilizando a classe [TaskOutputStorage][net_taskoutputstorage] encontrada na biblioteca de Convenções de Ficheiros.

No seu código de tarefa, primeiro crie um objeto [TaskOutputStorage,][net_taskoutputstorage] depois quando a tarefa tiver concluído o seu trabalho, ligue para o [TaskOutputStorage][net_taskoutputstorage]. [Guarde o método SaveAsync][net_saveasync] para guardar a sua saída para o Azure Storage.

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

O `kind` parâmetro da [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ O método SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) categoriza os ficheiros persistidos. Existem quatro tipos pré-definidos [taskOutputKind:][net_taskoutputkind] `TaskOutput` , , e também pode definir `TaskPreview` `TaskLog` `TaskIntermediate.` categorias de saída personalizadas.

Estes tipos de saída permitem especificar que tipo de saídas listar quando consultar mais tarde o Batch para as saídas persistiu de uma determinada tarefa. Por outras palavras, quando lista as saídas para uma tarefa, pode filtrar a lista num dos tipos de saída. Por exemplo, "Dê-me a *saída de pré-visualização* para a tarefa *109*." Mais sobre a listagem e recuperação de saídas aparece na saída Recuperar mais tarde no artigo.

> [!TIP]
> O tipo de saída também determina onde no portal Azure aparece um ficheiro específico: Ficheiros categorizados *taskOutput*-categorizados aparecem nos **ficheiros de saída de tarefas**, e os ficheiros *TaskLog* aparecem nos **registos de tarefas**.

### <a name="store-job-outputs"></a>Saídas de trabalho da loja

Além de armazenar saídas de tarefas, pode armazenar as saídas associadas a um trabalho completo. Por exemplo, na tarefa de fusão de um trabalho de renderização de filmes, você poderia persistir o filme totalmente renderizado como uma saída de trabalho. Quando o seu trabalho estiver concluído, a sua aplicação ao cliente pode listar e recuperar as saídas para o trabalho, e não precisa de consultar as tarefas individuais.

Armazenar saída de trabalho chamando o [JobOutputStorage][net_joboutputstorage]. [Método SaveAsync][net_joboutputstorage_saveasync] e especificar o [JobOutputKind][net_joboutputkind] e o nome de ficheiro:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Tal como acontece com o tipo **TaskOutputKind** para saídas de tarefas, utiliza o tipo [JobOutputKind][net_joboutputkind] para categorizar os ficheiros persistidos de um trabalho. Este parâmetro permite-lhe consultar mais tarde (lista) um tipo específico de saída. O tipo **JobOutputKind** inclui categorias de saída e pré-visualização, e suporta a criação de categorias personalizadas.

### <a name="store-task-logs"></a>Armazenar registos de tarefas

Além de persistir um ficheiro para armazenamento durável quando uma tarefa ou trabalho estiver concluído, poderá ter de persistir ficheiros que sejam atualizados durante a execução de ficheiros de registo de &mdash; tarefas `stdout.txt` `stderr.txt` ou, por exemplo. Para o efeito, a biblioteca de convenções de ficheiros Azure Batch fornece o [TaskOutputStorage][net_taskoutputstorage]. [Método SaveTrackedAsync.][net_savetrackedasync] Com [o SaveTrackedAsync,][net_savetrackedasync]pode rastrear atualizações num ficheiro no nó (num intervalo que especifique) e persistir essas atualizações para o Azure Storage.

No seguinte corte de código, utilizamos [o SaveTrackedAsync][net_savetrackedasync] para atualizar `stdout.txt` no Azure Storage a cada 15 segundos durante a execução da tarefa:

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

A secção comentada `Code to process data and produce output file(s)` é um espaço reservado para o código que a sua tarefa normalmente executaria. Por exemplo, pode ter um código que descarrega dados do Azure Storage e realiza transformação ou cálculo nele. A parte importante deste corte é demonstrar como pode embrulhar este código num `using` bloco para atualizar periodicamente um ficheiro com [SaveTrackedAsync][net_savetrackedasync].

O agente de nó é um programa que funciona em cada nó na piscina e fornece a interface de comando e controlo entre o nó e o serviço Batch. A `Task.Delay` chamada é necessária no final deste bloco para garantir que o agente de nó tem tempo para descarregar `using` o conteúdo do padrão para o ficheiro stdout.txt no nó. Sem este atraso, é possível perder os últimos segundos de saída. Este atraso pode não ser necessário para todos os ficheiros.

> [!NOTE]
> Quando ativa o rastreio de ficheiros com **o SaveTrackedAsync,** apenas os *apêndices* ao ficheiro rastreado são percêem para o Azure Storage. Utilize este método apenas para rastrear ficheiros de registo não rotativos ou outros ficheiros que sejam escritos com operações de apêndice até ao final do ficheiro.

## <a name="retrieve-output-data"></a>Recuperar dados de saída

Quando recuperar a sua saída persistiu utilizando a biblioteca de convenções de ficheiros Azure Batch, fá-lo de forma orientada para o trabalho. Pode solicitar a saída para uma determinada tarefa ou trabalho sem precisar de saber o seu caminho no Azure Storage, ou mesmo o seu nome de ficheiro. Em vez disso, pode solicitar ficheiros de saída por tarefa ou identificação de trabalho.

O código que se segue é o que corta através das tarefas de um trabalho, imprime algumas informações sobre os ficheiros de saída para a tarefa e, em seguida, descarrega os seus ficheiros a partir do Armazenamento.

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

O portal Azure apresenta ficheiros de saída de tarefas e registos que são persistidos a uma conta de Armazenamento Azure ligada utilizando a [norma de Convenções de Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Você mesmo pode implementar estas convenções num idioma à sua escolha, ou pode utilizar a biblioteca de Convenções de Ficheiros nas suas aplicações .NET.

Para ativar a visualização dos seus ficheiros de saída no portal, deve satisfazer os seguintes requisitos:

1. Ligue uma conta de Armazenamento Azure à sua conta Batch.
1. Aderir às convenções de nomeação predefinidas para recipientes e ficheiros de armazenamento quando persistirem saídas. Pode encontrar a definição destas convenções na biblioteca de convenções de arquivo [README.][github_file_conventions_readme] Se utilizar a biblioteca [de convenções de ficheiros Azure Batch][nuget_package] para persistir a sua saída, os seus ficheiros são persistidos de acordo com a norma de Convenções de Ficheiros.

Para visualizar ficheiros de saída de tarefas e registos no portal Azure, navegue para a tarefa cuja saída está interessada, clique em **ficheiros de saída guardados** ou **registos guardados**. Esta imagem mostra os **ficheiros de saída guardados** para a tarefa com iD "007":

![Lâmina de saídas de tarefa no portal Azure][2]

## <a name="code-sample"></a>Exemplo de código

O projeto de amostra [persistOutputs][github_persistoutputs] é uma das amostras de código do [Azure Batch][github_samples] no GitHub. Esta solução Visual Studio demonstra como utilizar a biblioteca de convenções de ficheiros Azure Batch para persistir na saída da tarefa para armazenamento durável. Para executar a amostra, siga estes passos:

1. Abra o projeto no **Visual Studio 2019.**
2. Adicione **as suas credenciais** de conta de lote e armazenamento a **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Construa** (mas não corra) a solução. Restaurar quaisquer pacotes NuGet se solicitado.
4. Utilize o portal Azure para carregar um [pacote de aplicações](batch-application-packages.md) para **PersistOutputsTask**. Inclua os `PersistOutputsTask.exe` conjuntos e os seus conjuntos dependentes no pacote .zip, definir o ID da aplicação para "PersistOutputsTask", e a versão do pacote de aplicações para "1.0".
5. **Inicie** (executar) o projeto **PersistOutputs.**
6. Quando solicitado para escolher a tecnologia de persistência a utilizar para executar a amostra, introduza **1** para executar a amostra utilizando a biblioteca de Convenções de Ficheiros para persistir na saída da tarefa. 

## <a name="next-steps"></a>Passos seguintes

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obtenha a biblioteca de convenções de ficheiros de lote para .NET

A biblioteca de convenções de ficheiros de lote para .NET está disponível no [NuGet][nuget_package]. A biblioteca estende as aulas [cloudJob][net_cloudjob] e [CloudTask][net_cloudtask] com novos métodos. Consulte também a [documentação de referência](/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca de Convenções de Arquivos.

O [código-fonte][github_file_conventions] da biblioteca de convenções de ficheiros está disponível no GitHub no Microsoft Azure SDK para o repositório .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explore outras abordagens para persistência de dados de saída

- Consulte [a produção de trabalho e tarefa persistente para o Azure Storage](batch-task-output.md) para uma visão geral dos dados de tarefas e de trabalho persistentes.
- Consulte [os dados de tarefa persiste para o Azure Storage com a API do serviço Batch](batch-task-output-files.md) para aprender a usar a API do serviço Batch para persistir nos dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Guardou ficheiros de saída e guardou os selecionadores de registos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Lâmina de saídas de tarefa no portal Azure"
