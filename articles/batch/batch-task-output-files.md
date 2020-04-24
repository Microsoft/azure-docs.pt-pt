---
title: Persistir dados de saída para o Armazenamento Azure com serviço de lote API - Lote Azure
description: Aprenda a utilizar o serviço de lote API para persistir a tarefa do Lote e os dados de saída de trabalho para o Armazenamento Azure.
ms.topic: article
ms.date: 03/05/2019
ms.custom: seodec18
ms.openlocfilehash: 5fbbf75defcfe976e59d38ae76341e71feee9f53
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116473"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Persistir dados de tarefas para o Armazenamento Azure com o serviço de lote API

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

O serviço de lote API suporta dados de saída persistentes para o Armazenamento Azure para tarefas e tarefas de gestor de emprego que funcionam em piscinas com a configuração da máquina virtual. Ao adicionar uma tarefa, pode especificar um recipiente no Armazenamento Azure como destino para a saída da tarefa. O serviço 'Lote' escreve então quaisquer dados de saída para esse recipiente quando a tarefa estiver completa.

Uma vantagem em utilizar a API do serviço de lote para persistir a saída de tarefa é que não precisa de modificar a aplicação que a tarefa está a executar. Em vez disso, com algumas modificações na aplicação do seu cliente, pode persistir a saída da tarefa a partir do mesmo código que cria a tarefa.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando uso o serviço de lote API para persistir a saída de tarefa?

O Lote Azure fornece mais de uma forma de persistir a saída de tarefas. Usar o serviço de lote API é uma abordagem conveniente que é mais adequada a estes cenários:

- Pretende escrever código para persistir a saída de tarefas a partir da sua aplicação de cliente, sem modificar a aplicação que a sua tarefa está a executar.
- Você quer persistir a saída a partir de tarefas do Lote e tarefas de gestor de emprego em piscinas criadas com a configuração da máquina virtual.
- Você deve persistir a saída para um recipiente de armazenamento Azure com um nome arbitrário.
- Pretende persistir a saída de um contentor de armazenamento Azure nomeado de acordo com a norma de Convenções de [Ficheiros](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)de Lote . 

Se o seu cenário difere dos acima listados, poderá ter de considerar uma abordagem diferente. Por exemplo, o serviço de lote API não suporta atualmente a saída de streaming para o Armazenamento Azure enquanto a tarefa está em execução. Para transmitir a saída, considere a utilização da biblioteca de Convenções de Ficheiros de Lote, disponível para .NET. Para outras línguas, terá de implementar a sua própria solução. Para obter mais informações sobre outras opções para a persistência da saída de tarefas, consulte [a saída de trabalho e tarefa sinuosa para o Armazenamento Azure](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Criar um recipiente no Armazenamento Azure

Para persistir a saída de tarefas para o Armazenamento Azure, terá de criar um recipiente que sirva de destino para os seus ficheiros de saída. Crie o recipiente antes de executar a sua tarefa, de preferência antes de submeter o seu trabalho. Para criar o recipiente, utilize a biblioteca ou SDK de clientes de armazenamento de armazenamento apropriado. Para mais informações sobre as APIs de Armazenamento Azure, consulte a documentação do [Armazenamento Azure.](https://docs.microsoft.com/azure/storage/)

Por exemplo, se estiver a escrever a sua aplicação em C#, utilize a [biblioteca de clientes do Azure Storage para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo que se segue mostra como criar um recipiente:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obtenha uma assinatura de acesso partilhado para o recipiente

Depois de criar o recipiente, obtenha uma assinatura de acesso partilhado (SAS) com acesso por escrito ao recipiente. Um SAS proporciona acesso delegado ao contentor. O SAS concede acesso com um conjunto de permissões especificado e num intervalo de tempo especificado. O serviço Batch necessita de um SAS com permissões de escrita para escrever a saída de tarefas para o recipiente. Para obter mais informações sobre o SAS, consulte [Utilizar assinaturas \(de acesso partilhado SAS\) no Armazenamento Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando obtém um SAS utilizando as APIs de Armazenamento Azure, a API devolve uma corda de token SAS. Esta cadeia simbólica inclui todos os parâmetros do SAS, incluindo as permissões e o intervalo sobre o qual o SAS é válido. Para utilizar o SAS para aceder a um contentor no Armazenamento Azure, é necessário anexar a cadeia de token SAS ao recurso URI. O recurso URI, juntamente com o token SAS anexado, proporciona acesso autenticado ao Armazenamento Azure.

O exemplo que se segue mostra como obter uma corda de token SAS apenas para o recipiente, em seguida, anexa o SAS ao recipiente URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Especificar ficheiros de saída para a saída de tarefas

Para especificar ficheiros de saída para uma tarefa, crie uma coleção de objetos [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) e atribua-os à propriedade [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) quando criar a tarefa.

O exemplo de código C# seguinte cria uma tarefa `output.txt`que escreve números aleatórios para um ficheiro chamado . O exemplo cria um `output.txt` ficheiro de saída para ser escrito no recipiente. O exemplo também cria ficheiros de saída `std*.txt` para quaisquer ficheiros `stderr.txt`de registo que correspondam ao padrão de ficheiro _(por exemplo,_ `stdout.txt` e ). O URL do recipiente requer o SAS que foi criado anteriormente para o recipiente. O serviço Batch utiliza o SAS para autenticar o acesso ao contentor:

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

### <a name="specify-a-file-pattern-for-matching"></a>Especifique um padrão de ficheiro para correspondência

Quando especificar um ficheiro de saída, pode utilizar a propriedade [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) para especificar um padrão de ficheiro para correspondência. O padrão de ficheiro pode coincidir com ficheiros zero, um único ficheiro ou um conjunto de ficheiros criados pela tarefa.

A propriedade **FilePattern** suporta wildcards padrão `*` do sistema de ficheiros tais `**` como (para jogos não recursivos) e (para partidas recursivas). Por exemplo, a amostra de código acima `std*.txt` especifica o padrão de ficheiro para combinar não-recursivamente:

`filePattern: @"..\std*.txt"`

Para fazer o upload de um único ficheiro, especifique um padrão de ficheiro sem wildcards. Por exemplo, a amostra de código acima `output.txt`especifica o padrão de ficheiro para combinar:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especifique uma condição de upload

A propriedade [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) permite o upload condicional de ficheiros de saída. Um cenário comum é carregar um conjunto de ficheiros se a tarefa for bem sucedida, e um conjunto diferente de ficheiros se falhar. Por exemplo, é melhor fazer o upload de ficheiros de registo verboso apenas quando a tarefa falhar e sair com um código de saída não zero. Da mesma forma, só poderá fazer o upload dos ficheiros de resultados se a tarefa for bem sucedida, uma vez que esses ficheiros podem estar em falta ou incompletos se a tarefa falhar.

A amostra de código acima define a propriedade **UploadCondition** para **TaskCompletion**. Esta definição especifica que o ficheiro deve ser carregado após a conclusão das tarefas, independentemente do valor do código de saída.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras definições, consulte o enum de [OutputFileUploadCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition)

### <a name="disambiguate-files-with-the-same-name"></a>Ficheiros desambiguados com o mesmo nome

As tarefas num trabalho podem produzir ficheiros com o mesmo nome. Por `stdout.txt` exemplo, `stderr.txt` e são criados para cada tarefa que corre num trabalho. Como cada tarefa funciona no seu próprio contexto, estes ficheiros não entram em conflito no sistema de ficheiros do nó. No entanto, ao fazer o upload de ficheiros de várias tarefas para um recipiente partilhado, terá de desambiguar ficheiros com o mesmo nome.

A propriedade [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) especifica a bolha de destino ou o diretório virtual para ficheiros de saída. Você pode usar a propriedade **Path** para nomear o blob ou diretório virtual de modo a que os ficheiros de saída com o mesmo nome sejam nomeados exclusivamente no Armazenamento Azure. Usar o ID de tarefa no caminho é uma boa maneira de garantir nomes únicos e identificar facilmente ficheiros.

Se a propriedade **FilePattern** for definida para uma expressão wildcard, então todos os ficheiros que correspondam ao padrão são enviados para o diretório virtual especificado pela propriedade **Path.** Por exemplo, se `mycontainer`o recipiente for `mytask`, o ID `..\std*.txt`de tarefa é , e o padrão de ficheiro é , então os URIs absolutos para os ficheiros de saída em Armazenamento Azure serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se a propriedade **FilePattern** estiver definida para corresponder a um único nome de ficheiro, o que significa que não contém quaisquer caracteres wildcard, então o valor da propriedade **Path** especifica o nome blob totalmente qualificado. Se antecipar a nomeação de conflitos com um único ficheiro de várias tarefas, então inclua o nome do diretório virtual como parte do nome do ficheiro para desambiguar esses ficheiros. Por exemplo, defino a propriedade **Path** para incluir o ID de tarefa, o caracteres delimitador (tipicamente um corte para a frente) e o nome do ficheiro:

`path: taskId + @"/output.txt"`

As URIs absolutas dos ficheiros de saída para um conjunto de tarefas serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre diretórios virtuais no Armazenamento Azure, consulte [Lista rindo as bolhas num recipiente](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de upload de ficheiros

Se o upload de ficheiros de saída para o Armazenamento Azure falhar, então a tarefa passa para o estado **concluído** e a propriedade Deinformação de Execução de [Tarefas.FalhaA](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) propriedade de informação é definida. Examine a propriedade **FailureInformation** para determinar o erro ocorrido. Por exemplo, aqui está um erro que ocorre no upload de ficheiros se o recipiente não puder ser encontrado:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada upload de ficheiros, o Batch escreve `fileuploadout.txt` dois `fileuploaderr.txt`ficheiros de registo para o nó de cálculo, e . Pode examinar estes ficheiros de registo para saber mais sobre uma falha específica. Nos casos em que o upload do ficheiro nunca foi tentado, por exemplo porque a tarefa em si não podia ser executada, então estes ficheiros de registo não existirão.

## <a name="diagnose-file-upload-performance"></a>Diagnóstico de desempenho de upload de ficheiro

Os `fileuploadout.txt` registos de ficheiros fazem upload do progresso. Pode examinar este ficheiro para saber mais sobre quanto tempo os seus uploads de ficheiros estão a demorar. Tenha em mente que existem muitos fatores que contribuem para carregar o desempenho, incluindo o tamanho do nó, outra atividade no nó no momento do upload, se o recipiente alvo está na mesma região que a piscina do Lote, quantos nós estão a carregar para a conta de armazenamento ao mesmo tempo, e assim por diante.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Utilize o serviço de lote API com a norma De Convenções de Ficheiros de Lote

Quando persistir a saída de tarefas com a API do serviço de lote, pode nomear o seu recipiente de destino e bolhas como quiser. Também pode optar por nomeá-los de acordo com a norma de Convenções de [Ficheiros](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)de Lote . A norma Determina os nomes do contentor de destino e blob no Armazenamento Azure para um determinado ficheiro de saída com base nos nomes do trabalho e da tarefa. Se utilizar a norma De Convenções de Ficheiros para nomear ficheiros de saída, os seus ficheiros de saída estão disponíveis para visualização no [portal Azure](https://portal.azure.com).

Se estiver a desenvolver-se em C#, pode utilizar os métodos incorporados na biblioteca de Convenções de Ficheiros de [Lote para .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Esta biblioteca cria os recipientes devidamente nomeados e caminhos blob para você. Por exemplo, pode ligar para a API para obter o nome correto para o recipiente, com base no nome do trabalho:

```csharp
string containerName = job.OutputStorageContainerName();
```

Pode utilizar o método [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) para devolver um URL de assinatura de acesso partilhado (SAS) que é usado para escrever no recipiente. Em seguida, pode passar este SAS para o construtor [OutputFileBlobContainerDestination.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination)

Se estiver a desenvolver-se numa língua diferente de C#, terá de implementar o padrão das Convenções de Arquivo.

## <a name="code-sample"></a>Exemplo de código

O projeto de amostra [PersistOutputs][github_persistoutputs] é uma das amostras de [código do Lote Azure][github_samples] no GitHub. Esta solução Visual Studio demonstra como usar a biblioteca de clientes Batch para .NET persistir a saída de tarefas para armazenamento duradouro. Para executar a amostra, siga estes passos:

1. Abra o projeto no **Visual Studio 2019.**
2. Adicione **as suas credenciais** de conta de Lote e Armazenamento às **definições de Definições de Conta** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Construir** (mas não executar) a solução. Restaure quaisquer pacotes NuGet se solicitado.
4. Utilize o portal Azure para fazer upload de um pacote de [aplicações](batch-application-packages.md) para **persistOutputsTask**. Incluir `PersistOutputsTask.exe` os conjuntos e os seus conjuntos dependentes no pacote .zip, definir o ID da aplicação para "PersistOutputsTask", e a versão do pacote de aplicação para "1.0".
5. **Iniciar** (executar) o projeto **PersistOutputs.**
6. Quando solicitado a escolher a tecnologia de persistência a utilizar para executar a amostra, introduza **2** para executar a amostra utilizando a API do serviço de lote para persistir na saída de tarefas.
7. Se desejar, volte a executar a amostra, inserindo **3** para persistir a saída com a API do serviço de lote, e também para nomear o contentor de destino e o caminho blob de acordo com a norma das Convenções de Ficheiros.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a saída de tarefas persistente sinuosa mente com a biblioteca de Convenções de Ficheiros para .NET, consulte persist e dados de [tarefas para o Armazenamento De Azure com a biblioteca](batch-task-output-file-conventions.md)de Convenções de Ficheiros de Lote para .NET .
- Para obter informações sobre outras abordagens para a persistência de dados de saída em Azure Batch, consulte [a saída de trabalho e tarefa sinuosa para o Armazenamento Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
