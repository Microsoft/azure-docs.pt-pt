---
title: Manter a saída de trabalhos e tarefas para o armazenamento do Azure com o serviço de Batch API - Azure Batch | Documentos da Microsoft
description: Saiba como utilizar a API de serviço do Batch para manter a saída de tarefa e a tarefa de lote para o armazenamento do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 77d8504dba180cfb56807344e659d6b22231f931
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866645"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Manter os dados de tarefas para o armazenamento do Azure com a API de serviço do Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A API do serviço Batch suporta manter dados de saída para o armazenamento do Azure para tarefas e tarefas de Gestor de tarefas serem executadas em conjuntos com a configuração de máquina virtual. Quando adiciona uma tarefa, pode especificar um contentor no armazenamento do Azure como o destino para a saída da tarefa. O serviço Batch escreve, em seguida, os dados de saída para esse contentor quando a tarefa estiver concluída.

Uma vantagem de utilizar a API de serviço do Batch para manter a saída de tarefa é que não é necessário modificar a aplicação que a tarefa é executada. Em vez disso, com algumas modificações para a aplicação cliente, pode manter a saída da tarefa de dentro do mesmo código que cria a tarefa.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando utiliza a API de serviço do Batch para manter a saída de tarefa?

O Azure Batch fornece mais de uma forma de manter a saída de tarefa. Com a API de serviço do Batch é uma abordagem conveniente que seja mais adequada a esses cenários:

- Deseja escrever códigos para manter o resultado da tarefa na sua aplicação de cliente, sem modificar a aplicação que a tarefa está em execução.
- Pretende manter a saída de tarefas do Batch e tarefas do Gestor nos agrupamentos criados com a configuração de máquina virtual.
- Pretende manter a saída para um contentor de armazenamento do Azure com um nome arbitrário.
- Pretende manter a saída para um contentor de armazenamento do Azure com o nome em conformidade com o [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> A API do serviço Batch suporta dados persistentes de tarefas em execução em conjuntos criados com a configuração do serviço cloud. Para obter informações sobre tarefas persistentes de saída de agrupamentos de executar a configuração de serviços cloud, consulte [manter os dados de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros do Batch para .NET manter](batch-task-output-file-conventions.md).

Se o seu cenário é diferente das listados acima, terá de considerar uma abordagem diferente. Por exemplo, a API de serviço do Batch não suporta atualmente transmissão em fluxo de saída para o armazenamento do Azure enquanto a tarefa está em execução. A saída do stream, considere utilizar a biblioteca convenções de ficheiros do Batch, disponível para .NET. Para outros idiomas, terá de implementar sua própria solução. Para obter mais informações sobre outras opções para manter o resultado da tarefa, consulte [manter a saída de trabalhos e tarefas para o armazenamento do Azure](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Criar um contentor no armazenamento do Azure

Para manter a saída de tarefa ao armazenamento do Azure, terá de criar um contentor que funciona como o destino para os seus ficheiros de saída. Crie o contentor antes de executar a tarefa, preferencialmente, antes de submeter a tarefa. Para criar o contentor, utilize o SDK ou biblioteca de cliente de armazenamento do Azure adequada. Para obter mais informações sobre as APIs de armazenamento do Azure, consulte a [documentação de armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Por exemplo, se estiver a escrever seu aplicativo em c#, utilize o [biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo seguinte mostra como criar um contentor:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obter uma assinatura de acesso partilhado do contentor

Depois de criar o contentor, obtenha uma assinatura de acesso partilhado (SAS) com acesso de escrita ao contentor. Uma SAS fornece acesso delegado para o contentor. A SAS concede acesso com um conjunto especificado de permissões e ao longo de um intervalo de tempo especificado. O serviço Batch tem uma SAS com permissões de escrita para escrever o resultado da tarefa para o contentor. Para obter mais informações sobre SAS, consulte [Using de assinaturas de acesso partilhado \(SAS\) no armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando receber um SAS com as APIs de armazenamento do Azure, a API devolve uma cadeia de caracteres de token de SAS. Essa cadeia de caracteres de token inclui todos os parâmetros de SAS, incluindo as permissões e o intervalo durante o qual a SAS é válida. Para utilizar a SAS para aceder a um contentor no armazenamento do Azure, terá de acrescentar a cadeia de caracteres de token de SAS para o URI do recurso. O recurso URI, juntamente com o token SAS anexado, fornece acesso autenticado ao armazenamento do Azure.

O exemplo seguinte mostra como obter uma cadeia só de escrita de token de SAS do contentor, em seguida, acrescenta a SAS para o URI do contentor:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Especificar os ficheiros de saída para o resultado da tarefa

Para especificar os ficheiros de saída para uma tarefa, crie uma coleção de [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objetos e atribuí-lo para o [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) propriedade quando cria a tarefa.

O seguinte C# exemplo de código cria uma tarefa que grava números aleatórios num arquivo chamado `output.txt`. O exemplo cria um ficheiro de saída para `output.txt` de escrita ao contentor. O exemplo também cria ficheiros de saída para quaisquer ficheiros de registo que correspondam ao padrão de arquivo `std*.txt` (_por exemplo,_, `stdout.txt` e `stderr.txt`). O URL de contentor requer a SAS que foi criada anteriormente para o contentor. O serviço Batch utiliza a SAS para autenticar o acesso ao contentor:

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

Quando especificar um ficheiro de saída, pode utilizar o [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) propriedade para especificar um padrão de arquivo para correspondência. O padrão de ficheiros pode corresponder ao nenhum arquivo, um único arquivo ou um conjunto de ficheiros que são criados pela tarefa.

O **FilePattern** propriedade suporta carateres universais do sistema de ficheiros padrão, tal como `*` (para não recursiva corresponde a) e `**` (para recursiva corresponde). Por exemplo, o código de exemplo acima Especifica o padrão de ficheiro para corresponder `std*.txt` não recursivamente:

`filePattern: @"..\std*.txt"`

Para carregar um único arquivo, especifique um padrão de ficheiro com carateres não universais. Por exemplo, o código de exemplo acima Especifica o padrão de ficheiro para corresponder `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especifique uma condição de carregamento

O [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) propriedade permite condicional de carregamento de ficheiros de saída. Um cenário comum é carregar um conjunto de ficheiros se a tarefa tiver êxito e um conjunto diferente de ficheiros se falhar. Por exemplo, poderá carregar ficheiros de registo verboso, apenas quando a tarefa falha e será encerrada com um código de saída diferente de zero. Da mesma forma, poderá carregar ficheiros de resultado apenas se a tarefa for concluída com êxito, pois esses arquivos podem ser em falta ou incompletas se a tarefa falhar.

O exemplo de código acima conjuntos a **UploadCondition** propriedade **TaskCompletion**. Esta definição especifica que o ficheiro está a ser carregado depois de concluir as tarefas, independentemente do valor do código de saída.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras definições, consulte a [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Ambiguidade ficheiros com o mesmo nome

As tarefas numa tarefa podem produzir ficheiros que tenham o mesmo nome. Por exemplo, `stdout.txt` e `stderr.txt` são criados para cada tarefa executada numa tarefa. Uma vez que cada tarefa é executada no seu próprio contexto, estes ficheiros não entram em conflito no sistema de ficheiros do nó. No entanto, ao carregar ficheiros a partir de várias tarefas num contêiner compartilhado, precisará para eliminar a ambiguidade ficheiros com o mesmo nome.

O [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) propriedade especifica o blob de destino ou o diretório virtual para ficheiros de saída. Pode utilizar o **caminho** propriedade para mencionar o blob ou diretório virtual de forma que os ficheiros de saída com o mesmo nome são nomeados exclusivamente no armazenamento do Azure. Com o ID de tarefa no caminho é uma boa maneira de garantir nomes exclusivos e identificar facilmente os arquivos.

Se o **FilePattern** estiver definida como uma expressão com carateres universais, em seguida, todos os ficheiros que correspondam ao padrão são carregados para o diretório virtual especificado pela **caminho** propriedade. Por exemplo, se o contentor está `mycontainer`, a tarefa é ID `mytask`, e o padrão de arquivo é `..\std*.txt`, em seguida, os URIs absoluto para os ficheiros de saída no armazenamento do Azure será semelhante a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se o **FilePattern** estiver definida para corresponder a um nome de ficheiro único, que significa que não contém quaisquer carateres universais, em seguida, o valor da **caminho** propriedade especifica o nome do blob completamente qualificado. Se prevê a atribuição de nomes está em conflito com um único arquivo de várias tarefas, em seguida, inclua o nome do diretório virtual como parte do nome do ficheiro para eliminar a ambiguidade esses ficheiros. Por exemplo, definir o **caminho** propriedade para incluir o ID de tarefa, o caráter delimitador (normalmente, uma barra) e o nome do ficheiro:

`path: taskId + @"/output.txt"`

O absoluto URIs para os ficheiros de saída para um conjunto de tarefas será semelhante a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre os diretórios virtuais no armazenamento do Azure, consulte [listar os blobs num contentor](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de carregamento de ficheiros

Se carregar ficheiros de saída para o armazenamento do Azure falha, então a tarefa se move para o **concluído** estado e o [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) propriedade está definida. Examine os **FailureInformation** propriedade para determinar o erro ocorreu. Por exemplo, aqui está um erro que ocorre no carregamento de ficheiros, se não é possível localizar o contentor:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada carregamento de ficheiros, o Batch escreve dois ficheiros de registo para o nó de computação `fileuploadout.txt` e `fileuploaderr.txt`. Pode examinar esses arquivos de log para obter mais informações sobre uma falha específica. Em casos em que o carregamento de ficheiros foi nunca tentado, por exemplo porque não foi possível executar a tarefa propriamente dita, em seguida, estes ficheiros de registo não existirá.

## <a name="diagnose-file-upload-performance"></a>Diagnosticar o desempenho de carregamento de ficheiros

O `fileuploadout.txt` progresso do carregamento de registos do ficheiro. Pode examinar esse arquivo para saber mais sobre o tempo que os carregamentos de ficheiros estão a demorar. Tenha em atenção que existem vários fatores, para carregar o desempenho, incluindo o tamanho do nó, outra atividade no nó no momento do carregamento, se o contentor de destino está na mesma região que o conjunto do Batch, estão a carregar para o stora quantos nós contas de ge ao mesmo tempo e assim por diante.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Utilizar a API de serviço do Batch com o padrão de convenções de ficheiros do Batch

Quando manter a saída de tarefa com a API de serviço do Batch, pode nomear o seu contentor de destino e desejar de blobs. Também pode optar por nomeá-los de acordo com o [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). O padrão de convenções de ficheiros determina os nomes do contentor de destino e os BLOBs no armazenamento do Azure para um ficheiro de saída determinado com base nos nomes dos trabalhos e tarefas. Se usar o padrão de convenções de ficheiros à nomenclatura dos ficheiros de saída, em seguida, os ficheiros de saída estão disponíveis para visualização na [portal do Azure](https://portal.azure.com).

Se estiver a desenvolver em c#, pode utilizar os métodos integrados da [biblioteca de convenções de ficheiros do Batch para .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Esta biblioteca cria o caminhos dos BLOBs e contentores nomeadas apropriada para. Por exemplo, pode chamar a API para obter o nome correto para o contentor, com base no nome da tarefa:

```csharp
string containerName = job.OutputStorageContainerName();
```

Pode utilizar o [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) método para retornar um URL de assinatura (SAS) de acesso partilhado que é utilizado para escrever para o contentor. Em seguida, pode passar este SAS para o [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) construtor.

Se estiver a desenvolver num idioma diferente do c#, terá de implementar o padrão de convenções de ficheiros por conta própria.

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs] [ github_persistoutputs] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra como utilizar a biblioteca de cliente do Batch para .NET para manter a saída de tarefas para o armazenamento durável. Para executar o exemplo, siga estes passos:

1. Abra o projeto na **Visual Studio 2017**.
2. Adicionar o Batch e de armazenamento **credenciais da conta** ao **AccountSettings.settings** no projeto Samples.
3. **Criar** (mas não são executados) a solução. Restaure quaisquer pacotes de NuGet se lhe for pedido.
4. Utilizar o portal do Azure para carregar um [pacote de aplicação](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e respetivas assemblagens dependentes no pacote. zip, defina o ID da aplicação para "PersistOutputsTask" e a versão do pacote de aplicação para "1.0".
5. **Inicie** (executar) o **PersistOutputs** projeto.
6. Quando lhe for pedido para escolher a tecnologia de persistência para utilizar para a execução do exemplo, introduza **2** para executar o exemplo com a API de serviço do Batch para manter a saída de tarefa.
7. Se assim o desejar, executar novamente o exemplo, introduzir **3** para manter a saída com a API de serviço do Batch e também para mencionar o caminho de contentor e blob de destino de acordo com o padrão de convenções de ficheiros.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o resultado da tarefa persistente com a biblioteca convenções de ficheiros para .NET, consulte [manter os dados de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros do Batch para .NET](batch-task-output-file-conventions.md).
- Para obter informações sobre outras abordagens para persistir dados de saída no Azure Batch, consulte [manter a saída de trabalhos e tarefas para o armazenamento do Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
