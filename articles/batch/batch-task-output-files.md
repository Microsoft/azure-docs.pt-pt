---
title: Persistir dados de saída para Azure Storage com API de serviço batch
description: Saiba como utilizar a API do serviço Batch para persistir os dados de tarefa e saída de trabalho do Lote para o Azure Storage.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936933"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Persistir dados de tarefas para o Azure Storage com o serviço Batch API

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

O serviço Batch API suporta dados de saída persistentes para O Armazenamento Azure para tarefas e tarefas de gestor de emprego que funcionam em piscinas com a configuração da máquina virtual. Ao adicionar uma tarefa, pode especificar um recipiente no Azure Storage como o destino para a saída da tarefa. Em seguida, o serviço Batch escreve quaisquer dados de saída para esse recipiente quando a tarefa estiver concluída.

Uma vantagem em utilizar a API do serviço Batch para persistir na saída da tarefa é que não precisa de modificar a aplicação que a tarefa está a executar. Em vez disso, com algumas modificações na aplicação do seu cliente, pode persistir a saída da tarefa a partir do mesmo código que cria a tarefa.

> [!IMPORTANT]
> Os dados de tarefas persistentes para o Azure Storage com o serviço Batch API não funcionam com piscinas criadas antes [de 1 de fevereiro de 2018](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204).

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando é que uso a API de serviço de lote para persistir na saída da tarefa?

O Azure Batch fornece mais de uma forma de persistir na saída da tarefa. A utilização do serviço De lote API é uma abordagem conveniente que se adequa melhor a estes cenários:

- Pretende escrever código para persistir a saída de tarefa a partir da aplicação do seu cliente, sem modificar a aplicação que a sua tarefa está a executar.
- Pretende persistir na saída das tarefas do Batch e das tarefas de gestor de emprego em piscinas criadas com a configuração da máquina virtual.
- Pretende persistir na saída para um recipiente de armazenamento Azure com um nome arbitrário.
- Pretende persistir na saída para um recipiente de armazenamento Azure nomeado de acordo com a [norma de Convenções de Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files).

Se o seu cenário diferir dos listados acima, poderá ter de considerar uma abordagem diferente. Por exemplo, o serviço De lote API não suporta atualmente a saída de streaming para o Azure Storage enquanto a tarefa estiver em execução. Para transmitir a saída, considere a utilização da biblioteca de Convenções de Ficheiros de Lote, disponível para .NET. Para outras línguas, terá de implementar a sua própria solução. Para obter informações sobre outras opções para a saída de tarefas persistentes, consulte [a saída de trabalho e tarefa persistente para o Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Criar um recipiente no Azure Storage

Para persistir na saída da tarefa para o Azure Storage, terá de criar um recipiente que sirva de destino para os seus ficheiros de saída. Crie o recipiente antes de executar a sua tarefa, de preferência antes de submeter o seu trabalho. Para criar o recipiente, utilize a biblioteca de clientes Azure Storage ou SDK apropriado. Para obter mais informações sobre as APIs de Armazenamento Azure, consulte a documentação de [armazenamento Azure](../storage/index.yml).

Por exemplo, se estiver a escrever a sua aplicação em C#, utilize a biblioteca de [clientes Azure Storage para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo a seguir mostra como criar um recipiente:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obtenha uma assinatura de acesso compartilhado para o recipiente

Depois de criar o recipiente, obtenha uma assinatura de acesso partilhado (SAS) com acesso de escrita ao recipiente. Um SAS fornece acesso delegado ao contentor. O SAS concede acesso com um conjunto especificado de permissões e durante um intervalo de tempo especificado. O serviço Batch necessita de um SAS com permissões de escrita para escrever a saída da tarefa para o recipiente. Para obter mais informações sobre o SAS, consulte [utilização de assinaturas de acesso partilhado \( SAS \) no Azure Storage](../storage/common/storage-sas-overview.md).

Quando obtém um SAS utilizando as APIs de Armazenamento Azure, a API devolve uma corda simbólica SAS. Esta cadeia simbólica inclui todos os parâmetros do SAS, incluindo as permissões e o intervalo sobre o qual o SAS é válido. Para utilizar o SAS para aceder a um contentor no Azure Storage, é necessário anexar a cadeia de fichas SAS ao recurso URI. O recurso URI, juntamente com o token SAS anexado, fornece acesso autenticado ao Azure Storage.

O exemplo a seguir mostra como obter uma corda simbólica SAS apenas para o recipiente e, em seguida, anexa o SAS ao contentor URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Especificar ficheiros de saída para a saída da tarefa

Para especificar ficheiros de saída para uma tarefa, crie uma coleção de objetos [DodeseFile](/dotnet/api/microsoft.azure.batch.outputfile) e atribua-os à propriedade [CloudTask.OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) quando criar a tarefa.

O exemplo de código C# a seguir cria uma tarefa que escreve números aleatórios num ficheiro chamado `output.txt` . O exemplo cria um ficheiro de saída `output.txt` para ser escrito no recipiente. O exemplo também cria ficheiros de saída para quaisquer ficheiros de registo que correspondam ao padrão de ficheiro `std*.txt` _(por exemplo,_ `stdout.txt` `stderr.txt` e). O URL do contentor requer o SAS que foi criado anteriormente para o recipiente. O serviço Batch utiliza o SAS para autenticar o acesso ao contentor:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Se utilizar este exemplo com o Linux, certifique-se de que muda as costas para as barras dianteiras.

### <a name="specify-a-file-pattern-for-matching"></a>Especifique um padrão de ficheiro para combinar

Quando especificar um ficheiro de saída, pode utilizar a propriedade [OutputFile.FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) para especificar um padrão de ficheiro para combinar. O padrão de ficheiro pode coincidir com ficheiros zero, um único ficheiro ou um conjunto de ficheiros que são criados pela tarefa.

A propriedade **FilePattern** suporta wildcards padrão do sistema de ficheiros, tais como `*` (para partidas não recursivas) e `**` (para partidas recursivas). Por exemplo, a amostra de código acima especifica o padrão de ficheiro para corresponder `std*.txt` não recursivamente:

`filePattern: @"..\std*.txt"`

Para fazer o upload de um único ficheiro, especifique um padrão de ficheiro sem wildcards. Por exemplo, a amostra de código acima especifica o padrão de ficheiro para `output.txt` corresponder:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especificar uma condição de upload

A [propriedade OutputFileUploadOptions.UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) permite o upload condicional de ficheiros de saída. Um cenário comum é carregar um conjunto de ficheiros se a tarefa for bem sucedida, e um conjunto diferente de ficheiros se falhar. Por exemplo, é de esperar que o upload de ficheiros de registo verboso apenas quando a tarefa falhar e sair com um código de saída não zero. Da mesma forma, pode querer fazer o upload dos ficheiros de resultados apenas se a tarefa for bem sucedida, uma vez que esses ficheiros podem estar em falta ou incompletos se a tarefa falhar.

A amostra de código acima define a propriedade **UploadCondition** para **TaskCompletion**. Esta definição especifica que o ficheiro deve ser carregado após o preenomento das tarefas, independentemente do valor do código de saída.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras definições, consulte o [enum de ResultadosFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Ficheiros desambiguados com o mesmo nome

As tarefas num trabalho podem produzir ficheiros com o mesmo nome. Por exemplo, `stdout.txt` e `stderr.txt` são criados para cada tarefa que funciona num trabalho. Como cada tarefa é executado no seu próprio contexto, estes ficheiros não entram em conflito no sistema de ficheiros do nó. No entanto, quando fizer o upload de ficheiros de várias tarefas para um recipiente partilhado, terá de desambiguar ficheiros com o mesmo nome.

A propriedade [OutputFileBlobContainerDestination.Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) especifica a bolha de destino ou diretório virtual para ficheiros de saída. Pode utilizar a propriedade **Path** para nomear o blob ou diretório virtual de tal forma que os ficheiros de saída com o mesmo nome sejam nomeados exclusivamente no Azure Storage. A utilização do ID de tarefa no caminho é uma boa forma de garantir nomes únicos e identificar facilmente ficheiros.

Se a propriedade **FilePattern** estiver definida como uma expressão wildcard, então todos os ficheiros que correspondam ao padrão são carregados para o diretório virtual especificado pela propriedade **Path.** Por exemplo, se o contentor for `mycontainer` , o ID de tarefa é `mytask` , e o padrão de ficheiro é `..\std*.txt` , então os URIs absolutos para os ficheiros de saída no Azure Storage serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se a propriedade **FilePattern** estiver definida para corresponder a um único nome de ficheiro, o que significa que não contém caracteres wildcard, então o valor da propriedade **Path** especifica o nome blob totalmente qualificado. Se antecipar o nome de conflitos com um único ficheiro de várias tarefas, então inclua o nome do diretório virtual como parte do nome do ficheiro para desambiguar esses ficheiros. Por exemplo, deite a propriedade **Path** para incluir o ID da tarefa, o personagem delimiter (normalmente um corte para a frente) e o nome do ficheiro:

`path: taskId + @"/output.txt"`

Os URIs absolutos aos ficheiros de saída para um conjunto de tarefas serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre diretórios virtuais no Azure Storage, consulte [listar as bolhas num recipiente.](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)

## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de upload de ficheiros

Se o upload de ficheiros de saída para O Azure Storage falhar, então a tarefa passa para o estado **preenchido** e a propriedade [TaskExecutionInformation.FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) é definida. Examine a propriedade **Falhainform** para determinar que erro ocorreu. Por exemplo, aqui está um erro que ocorre no upload de ficheiros se o contentor não for encontrado:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada upload de ficheiros, o Batch escreve dois ficheiros de registo para o nó de computação `fileuploadout.txt` e `fileuploaderr.txt` . Pode examinar estes ficheiros de registo para saber mais sobre uma falha específica. Nos casos em que o upload do ficheiro nunca foi tentado, por exemplo porque a tarefa em si não podia ser executada, então estes ficheiros de registo não existirão.

## <a name="diagnose-file-upload-performance"></a>Diagnosticar desempenho do upload de ficheiros

Os `fileuploadout.txt` registos de ficheiros fazem o upload do progresso. Pode examinar este ficheiro para saber mais sobre quanto tempo os uploads dos seus ficheiros estão a demorar. Tenha em mente que existem muitos fatores que contribuem para o desempenho do upload, incluindo o tamanho do nó, outra atividade no nó no momento do upload, se o contentor-alvo está na mesma região que o pool do Lote, quantos nós estão a carregar para a conta de armazenamento ao mesmo tempo, e assim por diante.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Utilize a API de serviço de lote com a norma de Convenções de Ficheiros de Lote

Quando persistir na saída de tarefa com a API de serviço de lote, pode nomear o seu recipiente de destino e bolhas como quiser. Também pode optar por nomeá-los de acordo com a [norma de Convenções de Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). A norma de Convenções de Ficheiros determina os nomes do contentor de destino e blob no Azure Storage para um dado ficheiro de saída com base nos nomes do trabalho e da tarefa. Se utilizar a norma "Convenções de Ficheiros" para nomear ficheiros de saída, os seus ficheiros de saída estão disponíveis para visualização no [portal Azure](https://portal.azure.com).

Se estiver a desenvolver em C#, pode utilizar os métodos incorporados na biblioteca de [Convenções de Ficheiros de Lote para .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Esta biblioteca cria os contentores e caminhos de bolhas devidamente nomeados para si. Por exemplo, pode ligar para a API para obter o nome correto para o recipiente, com base no nome do trabalho:

```csharp
string containerName = job.OutputStorageContainerName();
```

Pode utilizar o método [CloudJobExtensions.GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) para devolver um URL de assinatura de acesso partilhado (SAS) que é usado para escrever no recipiente. Em seguida, pode passar este SAS para o [construtor outputFileBlobContainerDestination.](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination)

Se estiver a desenvolver-se numa língua diferente de C#, terá de implementar o padrão das Convenções de Ficheiros.

## <a name="code-sample"></a>Exemplo de código

O projeto de amostra [persistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) é uma das amostras de código do [Azure Batch](https://github.com/Azure/azure-batch-samples) no GitHub. Esta solução Visual Studio demonstra como utilizar a biblioteca de clientes Batch para .NET para persistir a saída da tarefa para armazenamento durável. Para executar a amostra, siga estes passos:

1. Abra o projeto no **Visual Studio 2019.**
2. Adicione **as suas credenciais** de conta de lote e armazenamento a **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Construa** (mas não corra) a solução. Restaurar quaisquer pacotes NuGet se solicitado.
4. Utilize o portal Azure para carregar um [pacote de aplicações](batch-application-packages.md) para **PersistOutputsTask**. Inclua os `PersistOutputsTask.exe` conjuntos e os seus conjuntos dependentes no pacote .zip, definir o ID da aplicação para "PersistOutputsTask", e a versão do pacote de aplicações para "1.0".
5. **Inicie** (executar) o projeto **PersistOutputs.**
6. Quando solicitado para escolher a tecnologia de persistência a utilizar para executar a amostra, introduza **2** para executar a amostra utilizando a API de serviço de lote para persistir na saída da tarefa.
7. Se desejar, volte a executar a amostra, entrando **em 3** para persistir na saída com a API de serviço de lote, e também para nomear o contentor de destino e o caminho do blob de acordo com a norma das Convenções de Arquivo.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a persistência na saída de tarefas com a biblioteca de Convenções de Ficheiros para .NET, consulte [os dados de trabalho e tarefa de Persistência para O Armazenamento do Azure com a biblioteca de Convenções de Ficheiros de Lote para .NET](batch-task-output-file-conventions.md).
- Para saber mais sobre outras abordagens para a persistência de dados de saída em Azure Batch, consulte [a produção de trabalho e tarefa persistente para o Azure Storage](batch-task-output.md).
