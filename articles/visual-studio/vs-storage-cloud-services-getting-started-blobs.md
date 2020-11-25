---
title: Começar com o armazenamento de blob usando Visual Studio (serviços em nuvem)
description: Como começar a usar o armazenamento Azure Blob num projeto de serviço em nuvem em Visual Studio depois de se ligar a uma conta de armazenamento usando serviços conectados visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96007048"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução aos serviços ligados (projetos de serviços cloud) Armazenamento de Blobs do Azure e o Visual Studio
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar com o Azure Blob Storage depois de ter criado ou referenciado uma conta de Armazenamento Azure utilizando o diálogo visual Studio **Add Connected Services** num projeto de serviços em nuvem do Estúdio Visual. Vamos mostrar-lhe como aceder e criar recipientes blob, e como executar tarefas comuns como carregar, listar e descarregar bolhas. As amostras são escritas em C \# e utilizam a Biblioteca do Cliente de Armazenamento microsoft [Azure para .NET](/previous-versions/azure/dn261237(v=azure.100)).

O Azure Blob Storage é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Uma única bolha pode ser qualquer tamanho. As bolhas podem ser coisas como imagens, ficheiros de áudio e vídeo, dados brutos e ficheiros de documentos.

Tal como os ficheiros vivem em pastas, as bolhas de armazenamento vivem em contentores. Depois de criar um armazenamento, cria um ou mais recipientes no armazenamento. Por exemplo, num armazenamento chamado "Scrapbook", pode criar contentores no armazenamento chamados "imagens" para armazenar imagens e outro chamado "áudio" para armazenar ficheiros áudio. Depois de criar os recipientes, pode enviar-lhes ficheiros de bolhas individuais.

* Para obter mais informações sobre bolhas manipuladoras programáticas, consulte [Começar com o armazenamento Azure Blob usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Para obter informações gerais sobre o Armazenamento Azure, consulte [a documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/).
* Para obter informações gerais sobre os Serviços Azure Cloud, consulte [a documentação dos Serviços cloud](https://azure.microsoft.com/documentation/services/cloud-services/).
* Para obter mais informações sobre a programação ASP.NET aplicações, consulte [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Recipientes blob de acesso em código
Para aceder programaticamente a blobs em projetos de serviços na nuvem, é necessário adicionar os seguintes itens, se ainda não estiverem presentes.

1. Adicione as seguintes declarações de espaço de nome de código no topo de qualquer ficheiro C# no qual deseja aceder programáticamente ao Azure Storage.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações da sua cadeia de ligação de armazenamento e conta de armazenamento a partir da configuração do serviço Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Obtenha um objeto **CloudBlobClient** para fazer referência a um recipiente existente na sua conta de armazenamento.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Obtenha um objeto **CloudBlobContainer** para fazer referência a um recipiente de bolhas específico.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Utilize todo o código indicado no procedimento anterior em frente ao código indicado nas seguintes secções.
> 
> 

## <a name="create-a-container-in-code"></a>Criar um recipiente em código
> [!NOTE]
> Algumas APIs que realizam chamadas para o Azure Storage em ASP.NET são assíncronas. Consulte [a programação Asynchronous com Async e aguarde](/previous-versions/hh191443(v=vs.140)) mais informações. O código no exemplo a seguir pressupõe que está a utilizar métodos de programação async.
> 
> 

Para criar um recipiente na sua conta de armazenamento, tudo o que precisa de fazer é adicionar uma chamada ao **CreateIfNotExistsAsync** como no seguinte código:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Para disponibilizar os ficheiros dentro do recipiente a todos, pode definir o recipiente para ser público utilizando o seguinte código.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Qualquer pessoa na Internet pode ver bolhas num recipiente público, mas só pode modificá-las ou eliminá-las se tiver a chave de acesso apropriada.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O Azure Storage suporta bolhas de blocos e bolhas de página. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o método **UploadFromStream**. Esta operação cria o blob caso não exista, ou substitui se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **ListBlobs** do contentor para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao rico conjunto de propriedades e métodos para um **IListBlobItem** devolvido, deve lanhá-lo a um **cloudBlockBlob,** **CloudPageBlob** ou objeto **CloudBlobDirectory.** Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor **photos**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Como mostrado na amostra de código anterior, o serviço blob tem o conceito de diretórios dentro de contentores, também. Isto é para que possa organizar as suas bolhas numa estrutura mais semelhante a uma pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos num contentor com o nome **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Quando liga para **ListBlobs** no recipiente (como na amostra anterior), a recolha devolvida contém objetos **CloudBlobDirectory** e **CloudBlockBlob** que representam os diretórios e bolhas contidos no nível superior. Aqui está a saída resultante:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como **true**. Isto resulta em cada blob ser devolvido como um **CloudBlockBlob,** independentemente do diretório. Aqui está a chamada para **ListBlobs:**

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

e aqui estão os resultados:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Transferir blobs
Para transferir blobs, obtenha primeiro uma referência de blob e, em seguida, chame o método **DownloadToStream**. O exemplo seguinte utiliza o método **DownloadToStream** para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Também pode utilizar o método **DownloadToStream** para transferir os conteúdos de um blob como uma cadeia de texto.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Eliminar blobs
Para eliminar uma bolha, primeiro obtenha uma referência blob e, em seguida, chame o método **Eliminar.**

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas de forma assíncrona
Se está a listar um grande número de blobs ou pretende controlar o número de resultados devolvido numa operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como devolver resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera por devolver um grande conjunto de resultados.

Este exemplo mostra uma lista de blobs não hierárquica, mas também pode criar uma lista hierárquica ao definir o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** como **falso**.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido pela palavra-chave **async** e tem de devolver um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de exemplo até que a tarefa de listagem seja concluída.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]