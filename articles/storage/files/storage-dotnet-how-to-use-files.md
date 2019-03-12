---
title: Programar para os Ficheiros do Azure com .NET | Microsoft Docs
description: Saiba como programar aplicações e serviços .NET que utilizam os Ficheiros do Azure para armazenar dados de ficheiros.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/22/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 9238b1dfc697f9efae723aba787982ae1493a608
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762267"
---
# <a name="develop-for-azure-files-with-net"></a>Programar para os Ficheiros do Azure com .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Este tutorial demonstra noções básicas de utilização do .NET para programar aplicações que utilizam o serviço [Ficheiros do Azure](storage-files-introduction.md) para armazenar dados de ficheiros. Este tutorial cria uma aplicação de consola simples para executar ações básicas com o .NET e o serviço Ficheiros do Azure:

* Obter os conteúdos de um ficheiro
* Definir a quota (de tamanho máximo) para a partilha de ficheiros.
* Criar uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.
* Copiar um ficheiro para outro ficheiro na mesma conta de armazenamento.
* Copiar um ficheiro para um blob na mesma conta de armazenamento.
* Utilizar as Métricas do Storage do Azure para a resolução de problemas

Para saber mais sobre o serviço Ficheiros do Azure, veja [Introdução ao serviço Ficheiros do Azure](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Noções sobre as APIs de .NET

Os ficheiros do Azure fornece duas abordagens amplas para aplicações de cliente: Server Message Block (SMB) e REST. No âmbito do .NET, estas abordagens são resumidas pelas APIs `System.IO` e `WindowsAzure.Storage`.

API | Quando utilizar | Notas
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | A sua aplicação: <ul><li>Precisa de ler/escrever ficheiros através de SMB</li><li>Está em execução num dispositivo que tem acesso à sua conta do serviço Ficheiros do Azure através da porta 445</li><li>Não precisa de gerir qualquer definição administrativa da partilha de ficheiros</li></ul> | Codificar a E/S de ficheiros com o serviço Ficheiros do Azure via SMB é, de um modo geral, igual a codificar a E/S com qualquer partilha de ficheiros de rede ou dispositivo de armazenamento local. Veja [este tutorial](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) para obter uma introdução às inúmeras funcionalidades do .NET, incluindo a E/S de ficheiros.
[WindowsAzure.Storage](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet#client-library) | A sua aplicação: <ul><li>Não consegue aceder ao serviço Ficheiros do Azure através de SMB na porta 445 devido a restrições de ISP ou a uma firewall</li><li>Precisa de funcionalidade administrativa, como a capacidade de definir a quota de uma partilha de ficheiros ou criar uma assinatura de acesso partilhado</li></ul> | Este artigo demonstra a utilização de `WindowsAzure.Storage` para a E/S de ficheiros através de REST (em vez de SMB) e a gestão da partilha de ficheiros.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar a aplicação de consola e obter a assemblagem
No Visual Studio, crie uma nova aplicação de consola do Windows. Os passos seguintes mostram como criar uma aplicação de consola no Visual Studio 2017, no entanto, os passos são semelhantes em outras versões do Visual Studio.

1. Selecione **Ficheiro** > **Novo** > **Projeto**
2. Selecione **Instalado** > **Modelos** > **Visual C#** > **Ambiente de Trabalho Clássico do Windows**
3. Selecione **Aplicação da Consola (.NET Framework)**
4. Introduza um nome para a sua aplicação no campo **Nome:**
5. Selecione **OK**

Todos os exemplos de código deste tutorial podem ser adicionados ao método `Main()` do ficheiro `Program.cs` da aplicação da consola.

Pode utilizar a Biblioteca de Clientes de Armazenamento do Azure em qualquer tipo de aplicação .NET, incluindo um serviço cloud do Azure ou aplicação Web, e aplicações de ambiente de trabalho ou móveis. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

## <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários
Há dois pacotes que tem de fazer referência no seu projeto para concluir este tutorial:

* [Biblioteca de cliente de armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): Este pacote fornece acesso programático a recursos de dados na sua conta de armazenamento.
* [Biblioteca do Gestor de configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação está em execução.

Pode utilizar o NuGet para obter ambos os pacotes. Siga estes passos.

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.
2. Procure online “WindowsAzure.Storage” e clique em **Instalar** para instalar a Biblioteca de Clientes de Armazenamento e as respetivas dependências.
3. Procure online “WindowsAzure.ConfigurationManager” e clique em **Instalar** para instalar o Gestor de Configuração do Azure.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Guardar as suas credenciais da conta de armazenamento no ficheiro app.config
Em seguida, guarde as suas credenciais no ficheiro app.config do seu projeto. Edite o ficheiro app.config para que seja apresentado de forma semelhante ao seguinte exemplo, substituindo `myaccount` pelo seu nome da sua conta de armazenamento e `mykey` pela chave da mesma.

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
Abra o ficheiro `Program.cs` a partir do Explorador de Soluções e adicione as seguintes diretivas de utilização à parte superior do ficheiro.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Aceder à partilha de ficheiros programaticamente
Em seguida, adicione o seguinte código ao método `Main()` (depois do código mostrado acima) para obter a cadeia de ligação. Este código obtém uma referência para o ficheiro que criámos anteriormente e produz o respetivo conteúdo para a janela da consola.

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
A partir da versão 5.x da Biblioteca de Clientes do Armazenamento do Azure, pode definir a quota (ou o tamanho máximo) para uma partilha de ficheiros, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

Ao definir a quota para uma partilha, pode limitar o tamanho total dos ficheiros armazenados na partilha. Se o tamanho total dos ficheiros na partilha exceder a quota definida na partilha, os clientes não poderão aumentar o tamanho dos ficheiros existentes ou criar novos ficheiros, a menos que esses ficheiros estejam vazios.

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
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
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
A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. De igual modo, pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomenda-se a criação de uma política de acesso partilhado, uma vez que fornece um meio de revogação do SAS se este estiver comprometido.

O exemplo seguinte cria uma política de acesso partilhado numa partilha e, em seguida, utiliza essa política para fornecer as restrições para um SAS num ficheiro dentro da partilha.

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

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
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

Para obter mais informações sobre a criação e utilização de assinaturas de acesso partilhado, veja [Using Shared Access Signatures (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Utilizar Assinaturas de Acesso Partilhado [SAS]) e [Create and use a SAS with Azure Blobs](../blobs/storage-dotnet-shared-access-signature-part-2.md) (Criar e utilizar uma SAS com os Blobs do Azure).

## <a name="copy-files"></a>Copiar ficheiros
A partir da versão 5. x da Biblioteca de Clientes do Storage do Azure, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Nas secções seguintes, iremos demonstrar como realizar estas operações de cópia programaticamente.

Pode também utilizar o AzCopy para copiar um ficheiro para outro, copiar um blob para um ficheiro ou vice-versa. Veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se estiver a copiar um blob para um ficheiro ou um ficheiro para um blob, tem de utilizar uma assinatura de acesso partilhado (SAS) para autorizar o acesso ao objeto de origem, mesmo se estiver a copiar dentro da mesma conta de armazenamento.
> 
> 

**Copiar um ficheiro para outro ficheiro**: o exemplo seguinte copia um ficheiro para outro ficheiro na mesma partilha. Uma vez que esta operação de cópia copia entre ficheiros na mesma conta de armazenamento, pode utilizar a autenticação de Chave Partilhada para efetuar a cópia.

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

**Copiar um ficheiro para um blob**: o exemplo seguinte cria um ficheiro e copia-o para um blob na mesma conta de armazenamento. O exemplo cria um SAS para o ficheiro de origem, que o serviço utiliza para autorizar o acesso ao ficheiro de origem durante a operação de cópia.

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

## <a name="share-snapshots"></a>Instantâneos de partilha
A partir da versão 8.5 da biblioteca de cliente de armazenamento do Azure, pode criar um instantâneo de partilha. Também pode listar, procurar ou eliminar instantâneos de partilha. Os instantâneos de partilha são só de leitura, pelo que não são permitidas operações de escrita nos mesmos.

**Criar instantâneos de partilha**

O exemplo seguinte cria um instantâneo de partilha de ficheiros.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Listar instantâneos de partilha**

O exemplo seguinte lista os instantâneos de partilha numa partilha.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Procurar ficheiros e diretórios nos instantâneos de partilha**

O exemplo seguinte procura ficheiros e diretórios nos instantâneos de partilha.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Listar partilhas e instantâneos de partilha e restaurar partilhas de ficheiros ou ficheiros de instantâneos de partilha** 

Um instantâneo de uma partilha de ficheiros permite-lhe recuperar ficheiros individuais ou toda a partilha de ficheiros no futuro. 

Pode restaurar um ficheiro de um instantâneo de partilha de ficheiros através da consulta dos instantâneos de partilha de uma partilha de ficheiros. Em seguida, pode obter um ficheiro que pertence a um instantâneo de partilha específico e utilizar essa versão para ler e comparar diretamente ou restaurar.

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


**Eliminar instantâneos de partilha**

O exemplo seguinte elimina um instantâneo de partilha de ficheiros.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Utilizar métricas para resolver problemas de Ficheiros do Azure
Agora, a Análise de Armazenamento do Azure suporta métricas para os Ficheiros do Azure. Com os dados de métricas, pode rastrear pedidos e diagnosticar problemas.

Pode ativar métricas para os Ficheiros do Azure a partir do [Portal do Azure](https://portal.azure.com). Pode também ativar métricas programaticamente ao chamar a operação Definir Propriedades do Serviço do Ficheiro através da API REST ou de um dos respetivos análogos na Biblioteca de Clientes do Armazenamento.

O exemplo de código seguinte mostra como utilizar a Biblioteca de Clientes de Armazenamento para .NET, para ativar as métricas para os Ficheiros do Azure.

Primeiro, adicione as seguintes diretivas `using` ao ficheiro `Program.cs`, para além das que adicionou acima:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Tenha em atenção que, ao passo que os Blobs do Azure, a Tabela do Azure e as Filas do Azure utilizam o tipo `ServiceProperties` partilhado no espaço de nomes `Microsoft.WindowsAzure.Storage.Shared.Protocol`, os Ficheiros do Azure utilizam o seu próprio tipo, `FileServiceProperties`, no espaço de nomes `Microsoft.WindowsAzure.Storage.File.Protocol`. Contudo, ambos os espaços de nomes têm de ser referenciados a partir do seu código para o seguinte código compilar.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
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

Além disso, pode consultar o [Azure Files Troubleshooting Article (Artigo de Resolução de Problemas de Ficheiros do Azure)](storage-troubleshoot-windows-file-connection-problems.md) para obter documentação de orientação de resolução de problemas de ponto a ponto.

## <a name="next-steps"></a>Passos Seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos concetuais
* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Ficheiros do Azure: um sistema de ficheiros SMB na cloud sem incómodos para Windows e Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure Files with Linux (Como utilizar os Ficheiros do Azure com o Linux)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o Armazenamento de ficheiros
* [How to use AzCopy with Microsoft Azure Storage (Como utilizar o AzCopy com o Armazenamento do Microsoft Azure)](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Resolução de problemas de Ficheiros do Azure](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referência
* [Storage Client Library for .NET reference (Referência da Biblioteca de Clientes do Armazenamento para .NET)](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference (Referência da API REST do Serviço do Ficheiros)](https://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Publicações no blogue
* [Os Ficheiros do Azure já estão em disponibilidade geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure Files (Por dentro dos Ficheiros do Azure)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
