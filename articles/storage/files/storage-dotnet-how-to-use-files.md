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
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301442"
---
# <a name="develop-for-azure-files-with-net"></a>Programar para os Ficheiros do Azure com .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Este tutorial demonstra noções básicas de utilização do .NET para programar aplicações que utilizam o serviço [Ficheiros do Azure](storage-files-introduction.md) para armazenar dados de ficheiros. Este tutorial cria uma aplicação de consola simples para fazer ações básicas com .NET e Ficheiros Azure:

* Obtenha o conteúdo de um ficheiro.
* Detete o tamanho máximo ou *quota* para a parte do ficheiro.
* Crie uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utilize uma política de acesso armazenada definida na parte.
* Copiar um ficheiro para outro ficheiro na mesma conta de armazenamento.
* Copiar um ficheiro para um blob na mesma conta de armazenamento.
* Utilize métricas de armazenamento Azure para resolução de problemas.

Para saber mais sobre ficheiros Azure, veja [o que é ficheiros Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Noções sobre as APIs de .NET

O serviço Ficheiros do Azure fornece duas abordagens abrangentes no que se refere às aplicações cliente: SMB (Server Message Block) e REST. Dentro da .NET, os `System.IO` e `WindowsAzure.Storage` APIs abstraem estas abordagens.

API | Quando utilizar | Notas
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | A sua aplicação: <ul><li>Precisa de ler/escrever ficheiros utilizando SMB</li><li>Está em execução num dispositivo que tem acesso à sua conta do serviço Ficheiros do Azure através da porta 445</li><li>Não precisa de gerir qualquer definição administrativa da partilha de ficheiros</li></ul> | O ficheiro I/O implementado com Ficheiros Azure sobre SMB é geralmente o mesmo que I/O com qualquer partilha de ficheiros de rede ou dispositivo de armazenamento local. Para uma introdução a uma série de funcionalidades em .NET, incluindo ficheiro S/O, consulte o tutorial da Aplicação da [Consola.](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | A sua aplicação: <ul><li>Não é possível aceder a Ficheiros Azure utilizando SMB na porta 445 devido a restrições de firewall ou ISP</li><li>Precisa de funcionalidade administrativa, como a capacidade de definir a quota de uma partilha de ficheiros ou criar uma assinatura de acesso partilhado</li></ul> | Este artigo demonstra a utilização de `Microsoft.Azure.Storage.File` para ficheiros I/O utilizando o REST em vez de SMB e a gestão da parte do ficheiro.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar a aplicação de consola e obter a assemblagem

No Visual Studio, crie uma nova aplicação de consola do Windows. Os seguintes passos mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Inicie o Estúdio Visual e selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** escolha a App consola **(.NET Framework)** para C#, e, em seguida, selecione **Next**.
1. No **Configure o seu novo projeto,** insira um nome para a app e selecione **Criar**.

Pode adicionar todos os exemplos de código neste tutorial ao método `Main()` do ficheiro `Program.cs` da sua aplicação de consola.

Pode utilizar a biblioteca de clientes do Azure Storage em qualquer tipo de aplicação .NET. Estes tipos incluem um serviço de nuvem Azure ou web app, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

## <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

Consulte estes pacotes no seu projeto para completar este tutorial:

* [Biblioteca comum do Microsoft Azure Storage para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Este pacote fornece acesso programático a recursos comuns na sua conta de armazenamento.
* [Biblioteca Blob de Armazenamento Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Este pacote fornece acesso programático aos recursos blob na sua conta de armazenamento.
* [Biblioteca de ficheiros de armazenamento Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Este pacote fornece acesso programático aos recursos de arquivo na sua conta de armazenamento.
* [Biblioteca do Microsoft Azure Configuration Manager para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, onde quer que a sua aplicação seja executado.

Pode utilizar o NuGet para obter ambos os pacotes. Siga estes passos.

1. No **Solution Explorer,** clique no seu projeto e escolha **gerir pacotes NuGet**.
1. No **NuGet Package Manager,** selecione **Browse**. Em seguida, procure e escolha **Microsoft.Azure.Storage.Blob**, e, em seguida, **selecione Instalar**.

   Este passo instala o pacote e as suas dependências.
1. Procure e instale estes pacotes:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Guarde as credenciais da sua conta de armazenamento para o ficheiro App.config

Em seguida, guarde as suas credenciais no ficheiro `App.config` do seu projeto. No **Solution Explorer,** clique duas vezes `App.config` e edite o ficheiro de modo a que seja semelhante ao seguinte exemplo. Substitua `myaccount` pelo nome da sua conta de armazenamento e `mykey` com a chave da sua conta de armazenamento.

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

No **Solution Explorer,** abra o ficheiro `Program.cs` e adicione as seguintes diretivas utilizando as diretivas no topo do ficheiro.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Aceder à partilha de ficheiros programaticamente

Em seguida, adicione o seguinte conteúdo ao método `Main()`, após o código acima indicado, para recuperar a cadeia de ligação. Este código recebe uma referência ao ficheiro que criamos anteriormente e produz o seu conteúdo.

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

Começando pela versão 5.x da Biblioteca de Clientes de Armazenamento Azure, pode definir a quota (tamanho máximo) para uma partilha de ficheiros. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

A fixação da quota para uma ação limita o tamanho total dos ficheiros armazenados na parte. Se o tamanho total dos ficheiros na parte exceder a quota definida na parte, os clientes não podem aumentar o tamanho dos ficheiros existentes. Os clientes não podem criar novos ficheiros, a não ser que esses ficheiros estejam vazios.

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

A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. Também pode criar uma política de acesso armazenada numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomendamos a criação de uma política de acesso armazenada porque permite revogar o SAS se ficar comprometida.

O exemplo seguinte cria uma política de acesso armazenada numa ação. O exemplo utiliza essa política para fornecer os constrangimentos de um SAS num ficheiro da parte.

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

Para obter mais informações sobre a criação e utilização de assinaturas de acesso partilhado, consulte como funciona uma assinatura de [acesso partilhado.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Copiar ficheiros

A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Nas secções seguintes, demonstramos como fazer estas operações de cópia programáticamente.

Também pode utilizar o AzCopy para copiar um ficheiro para outro ou para copiar uma bolha para um ficheiro ou para o outro lado. Ver [Começar com AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se estiver a copiar um blob para um ficheiro ou um ficheiro para um blob, tem de utilizar uma assinatura de acesso partilhado (SAS) para autorizar o acesso ao objeto de origem, mesmo se estiver a copiar dentro da mesma conta de armazenamento.
>

### <a name="copy-a-file-to-another-file"></a>Copiar um ficheiro para outro ficheiro

O exemplo seguinte copia um ficheiro para outro ficheiro na mesma partilha. Uma vez que esta operação de cópia copia cópias entre ficheiros na mesma conta de armazenamento, pode utilizar a autenticação da Chave Partilhada para fazer a cópia.

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

O exemplo seguinte cria um ficheiro e copia-o para um blob na mesma conta do Storage. O exemplo cria um SAS para o ficheiro de origem, que o serviço utiliza para autorizar o acesso ao ficheiro de origem durante a operação de cópia.

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

## <a name="share-snapshots"></a>Partilhar instantâneos

Começando com a versão 8.5 da Biblioteca de Clientes de Armazenamento Azure, pode criar um instantâneo de partilha. Também pode listar, procurar ou eliminar instantâneos de partilha. Os instantâneos de partilha são só de leitura, pelo que não são permitidas operações de escrita nos mesmos.

### <a name="create-share-snapshots"></a>Criar instantâneos de partilha

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

### <a name="browse-files-and-directories-within-share-snapshots"></a>Navegue em ficheiros e diretórios dentro de instantâneos de partilha

O exemplo seguinte procura ficheiros e diretórios nos instantâneos de partilha.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Liste ações e partilhe instantâneos e restaure as partilhas de ficheiros ou ficheiros a partir de instantâneos de ações

Um instantâneo de uma partilha de ficheiros permite-lhe recuperar ficheiros individuais ou toda a partilha de ficheiros no futuro.

Pode restaurar um ficheiro de um instantâneo de partilha de ficheiros através da consulta dos instantâneos de partilha de uma partilha de ficheiros. Em seguida, pode recuperar um ficheiro que pertence a uma determinada fotografia de partilha. Utilize esta versão para ler e comparar diretamente ou restaurar.

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

### <a name="delete-share-snapshots"></a>Eliminar instantâneos de partilha

O exemplo seguinte elimina um instantâneo de partilha de ficheiros.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## Troubleshoot Azure Files usando métricas<a name="troubleshooting-azure-files-using-metrics"></a>

Agora, a Análise de Armazenamento do Azure suporta métricas para os Ficheiros do Azure. Com os dados de métricas, pode rastrear pedidos e diagnosticar problemas.

Pode ativar métricas para Ficheiros Azure a partir do [portal Azure](https://portal.azure.com). Também pode ativar as métricas programáticamente, ligando para a operação set File Service Properties com a API REST ou um dos seus analógicos na Biblioteca do Cliente de Armazenamento.

O exemplo de código seguinte mostra como utilizar a Biblioteca de Clientes de Armazenamento para .NET, para ativar as métricas para os Ficheiros do Azure.

Em primeiro lugar, adicione as seguintes diretivas `using` ao seu ficheiro `Program.cs`, juntamente com as que acrescentou acima:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Embora as filas Azure Blobs, Azure e Azure utilizem o tipo de `ServiceProperties` partilhado no espaço de `Microsoft.Azure.Storage.Shared.Protocol`, o Azure Files usa o seu próprio tipo, o tipo `FileServiceProperties` no espaço de nome `Microsoft.Azure.Storage.File.Protocol`. No entanto, deve referir ambos os espaços de nome do seu código para que o seguinte código seja compilado.

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

Se encontrar algum problema, pode consultar problemas de [Troubleshoot Azure Files no Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre os Ficheiros Azure, consulte os seguintes recursos:

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais

* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Ficheiros do Azure: um sistema de ficheiros SMB na cloud sem incómodos para Windows e Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Use ficheiros Azure com Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o File Storage

* [Começar com a AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Resolver problemas de Ficheiros do Azure no Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referência

* [APIs de armazenamento azure para .NET](/dotnet/api/overview/azure/storage)
* [Serviço de Arquivo REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Publicações no blogue

* [Armazenamento de ficheiros Azure, agora geralmente disponível](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Dentro do Armazenamento de Ficheiros do Azure)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Apresentando o Serviço de Ficheiros Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
