---
title: Manter saída de trabalho e tarefa no armazenamento do Azure com a API de serviço de lote – lote do Azure | Microsoft Docs
description: Saiba como usar a API do serviço de lote para persistir a saída de trabalho e tarefa do lote para o armazenamento do Azure.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e4a2af09b432961211a5f38ecd2d9dacd89d3868
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094446"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Manter os dados da tarefa no armazenamento do Azure com a API do serviço de lote

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A API do serviço de lote dá suporte à persistência de dados de saída no armazenamento do Azure para tarefas e tarefas do Gerenciador de trabalho que são executadas em pools com a configuração da máquina virtual. Ao adicionar uma tarefa, você pode especificar um contêiner no armazenamento do Azure como o destino da saída da tarefa. Em seguida, o serviço de lote grava todos os dados de saída nesse contêiner quando a tarefa é concluída.

Uma vantagem de usar a API do serviço de lote para manter a saída da tarefa é que você não precisa modificar o aplicativo que a tarefa está executando. Em vez disso, com algumas modificações no aplicativo cliente, você pode manter a saída da tarefa de dentro do mesmo código que cria a tarefa.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando usar a API do serviço de lote para manter a saída da tarefa?

O lote do Azure fornece mais de uma maneira de manter a saída da tarefa. Usar a API do serviço de lote é uma abordagem conveniente que é mais adequada para esses cenários:

- Você deseja escrever código para manter a saída da tarefa de dentro do seu aplicativo cliente, sem modificar o aplicativo que a tarefa está executando.
- Você deseja manter a saída das tarefas do lote e do Gerenciador de trabalho em pools criados com a configuração da máquina virtual.
- Você deseja manter a saída em um contêiner de armazenamento do Azure com um nome arbitrário.
- Você deseja manter a saída em um contêiner de armazenamento do Azure chamado de acordo com o [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

Se seu cenário for diferente daqueles listados acima, talvez seja necessário considerar uma abordagem diferente. Por exemplo, a API do serviço de lote atualmente não dá suporte à saída de streaming para o armazenamento do Azure enquanto a tarefa está em execução. Para transmitir a saída, considere usar a biblioteca de convenções de arquivo em lotes, disponível para .NET. Para outros idiomas, você precisará implementar sua própria solução. Para obter mais informações sobre outras opções para manter a saída da tarefa, consulte [manter a saída do trabalho e da tarefa no armazenamento do Azure](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Criar um contêiner no armazenamento do Azure

Para manter a saída da tarefa no armazenamento do Azure, você precisará criar um contêiner que serve como o destino para seus arquivos de saída. Crie o contêiner antes de executar a tarefa, preferivelmente antes de enviar seu trabalho. Para criar o contêiner, use a biblioteca de cliente de armazenamento do Azure ou o SDK apropriado. Para obter mais informações sobre as APIs de armazenamento do Azure, consulte a [documentação do armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Por exemplo, se você estiver gravando seu aplicativo C#no, use a [biblioteca de cliente de armazenamento do Azure para .net](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo a seguir mostra como criar um contêiner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obter uma assinatura de acesso compartilhado para o contêiner

Depois de criar o contêiner, obtenha uma SAS (assinatura de acesso compartilhado) com acesso de gravação ao contêiner. Uma SAS fornece acesso delegado ao contêiner. A SAS concede acesso com um conjunto especificado de permissões e em um intervalo de tempo especificado. O serviço de lote precisa de uma SAS com permissões de gravação para gravar a saída da tarefa no contêiner. Para obter mais informações sobre SAS, consulte [usando \(assinaturas de acesso compartilhado SAS\) no armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando você obtém uma SAS usando as APIs de armazenamento do Azure, a API retorna uma cadeia de caracteres de token SAS. Essa cadeia de caracteres de token inclui todos os parâmetros da SAS, incluindo as permissões e o intervalo sobre o qual a SAS é válida. Para usar a SAS para acessar um contêiner no armazenamento do Azure, você precisa acrescentar a cadeia de caracteres do token SAS ao URI do recurso. O URI de recurso, junto com o token SAS anexado, fornece acesso autenticado ao armazenamento do Azure.

O exemplo a seguir mostra como obter uma cadeia de caracteres de token SAS somente gravação para o contêiner e, em seguida, acrescenta a SAS ao URI do contêiner:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Especificar os arquivos de saída para a saída da tarefa

Para especificar arquivos de saída para uma tarefa, crie uma coleção [](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) de objetos OutputFile e atribua-o à propriedade [CloudTask. OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) ao criar a tarefa.

O exemplo C# de código a seguir cria uma tarefa que grava números aleatórios em `output.txt`um arquivo chamado. O exemplo cria um arquivo de saída `output.txt` para ser gravado no contêiner. O exemplo também cria arquivos de saída para todos os arquivos de log que correspondam ao `std*.txt` padrão de `stderr.txt`arquivo (_por exemplo_, `stdout.txt` e). A URL do contêiner requer a SAS que foi criada anteriormente para o contêiner. O serviço de lote usa a SAS para autenticar o acesso ao contêiner:

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

### <a name="specify-a-file-pattern-for-matching"></a>Especificar um padrão de arquivo para correspondência

Quando você especifica um arquivo de saída, você pode usar a propriedade [arquivo_de_saída. filepattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) para especificar um padrão de arquivo para correspondência. O padrão de arquivo pode corresponder a zero arquivos, um único arquivo ou um conjunto de arquivos que são criados pela tarefa.

A Propriedade filepattern dá suporte a curingas de sistema `*` de arquivos padrão, como (para correspondências `**` não recursivas) e (para correspondências recursivas). Por exemplo, o exemplo de código acima Especifica o padrão de arquivo `std*.txt` para corresponder de forma não recursiva:

`filePattern: @"..\std*.txt"`

Para carregar um único arquivo, especifique um padrão de arquivo sem curingas. Por exemplo, o exemplo de código acima Especifica o padrão de arquivo `output.txt`a ser correspondido:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especificar uma condição de carregamento

A propriedade [OutputFileUploadOptions. UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) permite o carregamento condicional de arquivos de saída. Um cenário comum é carregar um conjunto de arquivos se a tarefa for bem-sucedida e um conjunto diferente de arquivos se falhar. Por exemplo, talvez você queira carregar arquivos de log detalhados somente quando a tarefa falhar e sair com um código de saída diferente de zero. Da mesma forma, talvez você queira carregar arquivos de resultado somente se a tarefa for bem-sucedida, pois esses arquivos podem estar ausentes ou incompletos se a tarefa falhar.

O exemplo de código acima define a propriedade **UploadCondition** como **TaskCompletion**. Essa configuração especifica que o arquivo deve ser carregado após a conclusão das tarefas, independentemente do valor do código de saída.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras configurações, consulte a enumeração [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) .

### <a name="disambiguate-files-with-the-same-name"></a>Desambiguar arquivos com o mesmo nome

As tarefas em um trabalho podem produzir arquivos com o mesmo nome. Por exemplo, `stdout.txt` e `stderr.txt` são criados para cada tarefa executada em um trabalho. Como cada tarefa é executada em seu próprio contexto, esses arquivos não entram em conflito no sistema de arquivos do nó. No entanto, ao carregar arquivos de várias tarefas para um contêiner compartilhado, você precisará eliminar a ambiguidade de arquivos com o mesmo nome.

A propriedade [OutputFileBlobContainerDestination. path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) especifica o blob de destino ou o diretório virtual para arquivos de saída. Você pode usar a propriedade **path** para nomear o BLOB ou o diretório virtual de forma que os arquivos de saída com o mesmo nome sejam nomeados exclusivamente no armazenamento do Azure. O uso da ID da tarefa no caminho é uma boa maneira de garantir nomes exclusivos e identificar facilmente os arquivos.

Se a Propriedade filepattern for definida como uma expressão curinga, todos os arquivos que corresponderem ao padrão serão carregados no diretório virtual especificado pela propriedade **Path** . Por exemplo, se o contêiner for `mycontainer`, a ID da tarefa `mytask`for e o padrão do arquivo `..\std*.txt`for, os URIs absolutos para os arquivos de saída no armazenamento do Azure serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se a Propriedade filepattern estiver definida para corresponder a um único nome de arquivo, o que significa que ela não contém nenhum caractere curinga, o valor da propriedade **Path** especificará o nome do blob totalmente qualificado. Se você antecipar conflitos de nomenclatura com um único arquivo de várias tarefas, inclua o nome do diretório virtual como parte do nome do arquivo para eliminar a ambiguidade desses arquivos. Por exemplo, defina a propriedade **path** para incluir a ID da tarefa, o caractere delimitador (normalmente uma barra "e") e o nome do arquivo:

`path: taskId + @"/output.txt"`

Os URIs absolutos para os arquivos de saída para um conjunto de tarefas serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre diretórios virtuais no armazenamento do Azure, consulte [listar os BLOBs em um contêiner](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de upload de arquivo

Se o carregamento de arquivos de saída no armazenamento do Azure falhar, a tarefa será movida para o estado **concluído** e a propriedade [TaskExecutionInformation. FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) será definida. Examine a propriedade **FailureInformation** para determinar qual erro ocorreu. Por exemplo, aqui está um erro que ocorre no upload do arquivo se o contêiner não puder ser encontrado:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada upload de arquivo, o lote grava dois arquivos de log no nó `fileuploadout.txt` de `fileuploaderr.txt`computação e. Você pode examinar esses arquivos de log para saber mais sobre uma falha específica. Nos casos em que o carregamento do arquivo nunca foi tentado, por exemplo, porque a própria tarefa não pôde ser executada, esses arquivos de log não existirão.

## <a name="diagnose-file-upload-performance"></a>Diagnosticar o desempenho do upload de arquivo

O `fileuploadout.txt` progresso do upload dos logs de arquivo. Você pode examinar esse arquivo para saber mais sobre quanto tempo os carregamentos de arquivos estão levando. Tenha em mente que há muitos fatores que contribuem para carregar o desempenho, incluindo o tamanho do nó, outra atividade no nó no momento do carregamento, se o contêiner de destino está na mesma região que o pool do lote, quantos nós estão carregando para o Stora a conta GE ao mesmo tempo e assim por diante.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Usar a API do serviço de lote com o padrão de convenções de arquivo em lotes

Quando você mantém a saída da tarefa com a API do serviço de lote, você pode nomear o contêiner de destino e os BLOBs, como desejar. Você também pode optar por nomeá-los de acordo com o [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). O padrão de convenções de arquivo determina os nomes do contêiner de destino e do blob no armazenamento do Azure para um determinado arquivo de saída com base nos nomes do trabalho e da tarefa. Se você usar o padrão de convenções de arquivo para nomear arquivos de saída, os arquivos de saída estarão disponíveis para exibição no [portal do Azure](https://portal.azure.com).

Se estiver desenvolvendo no C#, você poderá usar os métodos criados na [biblioteca de convenções de arquivo em lotes para .net](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Essa biblioteca cria os contêineres nomeados e os caminhos de blob adequadamente para você. Por exemplo, você pode chamar a API para obter o nome correto para o contêiner, com base no nome do trabalho:

```csharp
string containerName = job.OutputStorageContainerName();
```

Você pode usar o método [CloudJobExtensions. GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) para retornar uma URL de SAS (assinatura de acesso compartilhado) que é usada para gravar no contêiner. Em seguida, você pode passar essa SAS para o construtor [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) .

Se você estiver desenvolvendo em um idioma diferente do C#, será necessário implementar o padrão de convenções de arquivo por conta própria.

## <a name="code-sample"></a>Exemplo de código

O projeto de exemplo [PersistOutputs][github_persistoutputs] é um dos [exemplos de código do lote do Azure][github_samples] no github. Esta solução do Visual Studio demonstra como usar a biblioteca de cliente do lote para .NET para manter a saída da tarefa para o armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2019**.
2. Adicione suas **credenciais de conta** de armazenamento e lote a **AccountSettings. Settings** no projeto Microsoft. Azure. Batch. Samples. Common.
3. **Criar** (mas não execute) a solução. Restaure todos os pacotes NuGet, se solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Inclua o `PersistOutputsTask.exe` e seus assemblies dependentes no pacote. zip, defina a ID do aplicativo como "PersistOutputsTask" e a versão do pacote de aplicativos como "1,0".
5. **Iniciar** (Execute) o projeto **PersistOutputs** .
6. Quando for solicitado a escolher a tecnologia de persistência a ser usada para executar o exemplo, digite **2** para executar o exemplo usando a API do serviço de lote para manter a saída da tarefa.
7. Se desejar, execute o exemplo novamente, inserindo **3** para manter a saída com a API do serviço de lote e também para nomear o contêiner de destino e o caminho do blob de acordo com o padrão de convenções de arquivo.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre como manter a saída da tarefa com a biblioteca de convenções de arquivo para .NET, consulte [manter dados de trabalho e tarefa no armazenamento do Azure com a biblioteca de convenções de arquivo em lotes para .net](batch-task-output-file-conventions.md).
- Para obter informações sobre outras abordagens para persistir dados de saída no lote do Azure, consulte [manter saída de trabalho e tarefa no armazenamento do Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
