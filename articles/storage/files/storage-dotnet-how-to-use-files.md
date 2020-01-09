---
title: Programar para os Ficheiros do Azure com .NET | Microsoft Docs
description: Saiba como programar aplicações e serviços .NET que utilizam os Ficheiros do Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dfb1d71a02ae3bf06a5f2d8a93bcb3ac83433a86
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460355"
---
# <a name="develop-for-azure-files-with-net"></a>Programar para os Ficheiros do Azure com .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Este tutorial demonstra noções básicas de utilização do .NET para programar aplicações que utilizam o serviço [Ficheiros do Azure](storage-files-introduction.md) para armazenar dados de ficheiros. Este tutorial cria um aplicativo de console simples para executar ações básicas com o .NET e os arquivos do Azure:

* Obter o conteúdo de um arquivo.
* Defina o tamanho máximo ou a *cota* para o compartilhamento de arquivos.
* Crie uma assinatura de acesso compartilhado (chave SAS) para um arquivo que usa uma política de acesso armazenada definida no compartilhamento.
* Copiar um ficheiro para outro ficheiro na mesma conta de armazenamento.
* Copiar um ficheiro para um blob na mesma conta de armazenamento.
* Use as métricas de armazenamento do Azure para solução de problemas.

Para saber mais sobre os arquivos do Azure, confira [o que são os arquivos do Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Noções sobre as APIs de .NET

O serviço Ficheiros do Azure fornece duas abordagens abrangentes no que se refere às aplicações cliente: SMB (Server Message Block) e REST. No .NET, as APIs `System.IO` e `WindowsAzure.Storage` abstraim essas abordagens.

API | Quando utilizar | Notas
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | A sua aplicação: <ul><li>Precisa ler/gravar arquivos usando SMB</li><li>Está em execução num dispositivo que tem acesso à sua conta do serviço Ficheiros do Azure através da porta 445</li><li>Não precisa de gerir qualquer definição administrativa da partilha de ficheiros</li></ul> | A e/s de arquivo implementada com os arquivos do Azure via SMB geralmente é a mesma de e/s com qualquer compartilhamento de arquivos de rede ou dispositivo de armazenamento local. Para obter uma introdução a vários recursos no .NET, incluindo e/s de arquivo, consulte o tutorial [aplicativo de console](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) .
[Microsoft.Azure.Storage.File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | A sua aplicação: <ul><li>Não é possível acessar os arquivos do Azure usando SMB na porta 445 devido a restrições de firewall ou ISP</li><li>Precisa de funcionalidade administrativa, como a capacidade de definir a quota de uma partilha de ficheiros ou criar uma assinatura de acesso partilhado</li></ul> | Este artigo demonstra o uso de `Microsoft.Azure.Storage.File` para e/s de arquivo usando REST em vez de SMB e gerenciamento do compartilhamento de arquivos.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar a aplicação de consola e obter a assemblagem

No Visual Studio, crie uma nova aplicação de consola do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Inicie o Visual Studio e selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** para C#e, em seguida, selecione **Avançar**.
1. Em **configurar seu novo projeto**, insira um nome para o aplicativo e selecione **criar**.

Você pode adicionar todos os exemplos de código neste tutorial ao método `Main()` do arquivo de `Program.cs` do seu aplicativo de console.

Você pode usar a biblioteca de cliente de armazenamento do Azure em qualquer tipo de aplicativo .NET. Esses tipos incluem um serviço de nuvem do Azure ou aplicativo Web, e aplicativos móveis e de desktop. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

## <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

Consulte estes pacotes em seu projeto para concluir este tutorial:

* [Armazenamento do Microsoft Azure biblioteca comum para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Este pacote fornece acesso programático a recursos comuns em sua conta de armazenamento.
* [Armazenamento do Microsoft Azure biblioteca de BLOBs para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Este pacote fornece acesso programático a recursos de BLOB em sua conta de armazenamento.
* [Biblioteca de arquivos Armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Este pacote fornece acesso programático a recursos de arquivo em sua conta de armazenamento.
* [Microsoft Azure Configuration Manager biblioteca para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Esse pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, onde quer que seu aplicativo seja executado.

Pode utilizar o NuGet para obter ambos os pacotes. Siga estes passos.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em seu projeto e escolha **gerenciar pacotes NuGet**.
1. No **Gerenciador de pacotes NuGet**, selecione **procurar**. Em seguida, pesquise e escolha **Microsoft. Azure. Storage. blob**e, em seguida, selecione **instalar**.

   Esta etapa instala o pacote e suas dependências.
1. Pesquise e instale esses pacotes:

   * **Microsoft. Azure. Storage. Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft. Azure. ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvar as credenciais da conta de armazenamento no arquivo app. config

Em seguida, salve suas credenciais no arquivo de `App.config` do seu projeto. Em **Gerenciador de soluções**, clique duas vezes em `App.config` e edite o arquivo para que ele seja semelhante ao exemplo a seguir. Substitua `myaccount` pelo nome da sua conta de armazenamento e `mykey` pela sua chave de conta de armazenamento.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> A versão mais recente do emulador de armazenamento do Azure não suporta os Ficheiros do Azure. A sua cadeia de ligação tem de visar uma Conta de Armazenamento do Azure na cloud, para funcionar com os Ficheiros do Azure.

## <a name="add-using-directives"></a>Adicionar com diretivas

No **Gerenciador de soluções**, abra o arquivo de `Program.cs` e adicione as seguintes diretivas using à parte superior do arquivo.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Aceder à partilha de ficheiros programaticamente

Em seguida, adicione o seguinte conteúdo ao método `Main()`, após o código mostrado acima, para recuperar a cadeia de conexão. Esse código obtém uma referência ao arquivo que criamos anteriormente e gera seu conteúdo.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Execute a aplicação de consola para ver o resultado.

## <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo para uma partilha de ficheiros

A partir da versão 5. x da biblioteca de cliente do armazenamento do Azure, você pode definir a cota (tamanho máximo) para um compartilhamento de arquivos. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

Definir a cota de um compartilhamento limita o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento exceder a cota definida no compartilhamento, os clientes não poderão aumentar o tamanho dos arquivos existentes. Os clientes não podem criar novos arquivos, a menos que esses arquivos estejam vazios.

O exemplo abaixo mostra como verificar a utilização atual para uma partilha e como configurar a quota para a partilha.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso partilhado para um ficheiro ou partilha de ficheiros

A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. Você também pode criar uma política de acesso armazenada em um compartilhamento de arquivos para gerenciar assinaturas de acesso compartilhado. É recomendável criar uma política de acesso armazenada porque ela permite revogar a SAS se ela for comprometida.

O exemplo a seguir cria uma política de acesso armazenada em um compartilhamento. O exemplo usa essa política para fornecer as restrições para uma SAS em um arquivo no compartilhamento.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Para obter mais informações sobre como criar e usar assinaturas de acesso compartilhado, consulte [como funciona uma assinatura de acesso compartilhado](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Copiar ficheiros

A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Nas próximas seções, demonstraremos como fazer essas operações de cópia programaticamente.

Você também pode usar AzCopy para copiar um arquivo para outro ou para copiar um blob para um arquivo ou o contrário. Consulte Introdução [ao AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se estiver a copiar um blob para um ficheiro ou um ficheiro para um blob, tem de utilizar uma assinatura de acesso partilhado (SAS) para autorizar o acesso ao objeto de origem, mesmo se estiver a copiar dentro da mesma conta de armazenamento.
>

### <a name="copy-a-file-to-another-file"></a>Copiar um ficheiro para outro ficheiro

O exemplo seguinte copia um ficheiro para outro ficheiro na mesma partilha. Como essa operação de cópia copia entre arquivos na mesma conta de armazenamento, você pode usar a autenticação de chave compartilhada para fazer a cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Copiar um ficheiro para um blob

O exemplo seguinte cria um ficheiro e copia-o para um blob na mesma conta de armazenamento. O exemplo cria um SAS para o ficheiro de origem, que o serviço utiliza para autorizar o acesso ao ficheiro de origem durante a operação de cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Pode copiar um blob para um ficheiro da mesma forma. Se o objeto de origem for um blob, crie um SAS para autorizar o acesso a esse blob durante a operação de cópia.

## <a name="share-snapshots"></a>Instantâneos de compartilhamento

A partir da versão 8,5 da biblioteca de cliente do armazenamento do Azure, você pode criar um instantâneo de compartilhamento. Também pode listar, procurar ou eliminar instantâneos de partilha. Os instantâneos de partilha são só de leitura, pelo que não são permitidas operações de escrita nos mesmos.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento

O exemplo seguinte cria um instantâneo de partilha de ficheiros.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Listar instantâneos de partilha

O exemplo seguinte lista os instantâneos de partilha numa partilha.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Procurar arquivos e diretórios em instantâneos de compartilhamento

O exemplo seguinte procura ficheiros e diretórios nos instantâneos de partilha.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Listar compartilhamentos e compartilhar instantâneos e restaurar compartilhamentos de arquivos ou arquivos de instantâneos de compartilhamento

Um instantâneo de uma partilha de ficheiros permite-lhe recuperar ficheiros individuais ou toda a partilha de ficheiros no futuro.

Pode restaurar um ficheiro de um instantâneo de partilha de ficheiros através da consulta dos instantâneos de partilha de uma partilha de ficheiros. Em seguida, você pode recuperar um arquivo que pertence a um instantâneo de compartilhamento específico. Use essa versão para ler e comparar diretamente ou para restaurar o.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Excluir instantâneos de compartilhamento

O exemplo seguinte elimina um instantâneo de partilha de ficheiros.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## Solucionar problemas de arquivos do Azure usando métricas<a name="troubleshooting-azure-files-using-metrics"></a>

Agora, a Análise de Armazenamento do Azure suporta métricas para os Ficheiros do Azure. Com os dados de métricas, pode rastrear pedidos e diagnosticar problemas.

Você pode habilitar as métricas para arquivos do Azure do [portal do Azure](https://portal.azure.com). Você também pode habilitar as métricas programaticamente chamando a operação definir propriedades do serviço de arquivo com a API REST ou uma de suas analogias na biblioteca do cliente de armazenamento.

O exemplo de código seguinte mostra como utilizar a Biblioteca de Clientes de Armazenamento para .NET, para ativar as métricas para os Ficheiros do Azure.

Primeiro, adicione as seguintes diretivas de `using` ao arquivo de `Program.cs`, juntamente com aquelas que você adicionou acima:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Embora os BLOBs do Azure, as tabelas do Azure e as filas do Azure usem o tipo de `ServiceProperties` compartilhado no namespace `Microsoft.Azure.Storage.Shared.Protocol`, os arquivos do Azure usam seu próprio tipo, o `FileServiceProperties` tipo no namespace `Microsoft.Azure.Storage.File.Protocol`. Você deve referenciar ambos os namespaces do seu código, no entanto, para que o código a seguir seja compilado.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Se você encontrar algum problema, poderá consultar [solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os arquivos do Azure, consulte os seguintes recursos:

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais

* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Ficheiros do Azure: um sistema de ficheiros SMB na cloud sem incómodos para Windows e Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o Armazenamento de ficheiros

* [Introdução ao AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Resolver problemas de Ficheiros do Azure no Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referência

* [APIs de Armazenamento do Microsoft Azure para .NET](/dotnet/api/overview/azure/storage)
* [API REST de Serviço de Ficheiros](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Publicações no blogue

* [Armazenamento de arquivos do Azure, agora disponível para o público geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Dentro do Armazenamento de Ficheiros do Azure)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introdução ao serviço de arquivos de Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
