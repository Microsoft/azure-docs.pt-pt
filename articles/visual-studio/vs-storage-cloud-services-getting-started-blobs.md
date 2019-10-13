---
title: Introdução ao armazenamento de BLOBs usando o Visual Studio (serviços de nuvem)
description: Como começar a usar o armazenamento de BLOBs do Azure em um projeto de serviço de nuvem no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d151e55f627166d8ad7d8affa53740e86cd1e501
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298801"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral
Este artigo descreve como começar a usar o armazenamento de BLOBs do Azure depois de criar ou referenciar uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio em um projeto de serviços de nuvem do Visual Studio. Mostraremos como acessar e criar contêineres de BLOB e como executar tarefas comuns, como carregar, listar e baixar BLOBs. Os exemplos são escritos em C @ no__t-0 e usam a [biblioteca de cliente armazenamento do Microsoft Azure para .net](https://msdn.microsoft.com/library/azure/dn261237.aspx).

O armazenamento de BLOBs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Os BLOBs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos.

Assim como os arquivos residem em pastas, os blobs de armazenamento residem em contêineres. Depois de criar um armazenamento, você cria um ou mais contêineres no armazenamento. Por exemplo, em um armazenamento chamado "recorte", você pode criar contêineres no armazenamento chamado "imagens" para armazenar imagens e outro chamado "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você pode carregar arquivos de blob individuais para eles.

* Para obter mais informações sobre como manipular BLOBs programaticamente, consulte Introdução [ao armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Para obter informações gerais sobre o armazenamento do Azure, consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/).
* Para obter informações gerais sobre os serviços de nuvem do Azure, consulte a [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/).
* Para obter mais informações sobre como programar aplicativos ASP.NET, consulte [ASP.net](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Acessar contêineres de blob no código
Para acessar programaticamente BLOBs em projetos de serviço de nuvem, você precisa adicionar os itens a seguir, se eles ainda não estiverem presentes.

1. Adicione as seguintes declarações de namespace de código à parte superior C# de qualquer arquivo no qual você deseja acessar o armazenamento do Azure programaticamente.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Obtenha um objeto **CloudBlobClient** para fazer referência a um contêiner existente em sua conta de armazenamento.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Obtenha um objeto **CloudBlobContainer** para fazer referência a um contêiner de blob específico.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Use todo o código mostrado no procedimento anterior na frente do código mostrado nas seções a seguir.
> 
> 

## <a name="create-a-container-in-code"></a>Criar um contêiner no código
> [!NOTE]
> Algumas APIs que executam chamadas para o armazenamento do Azure no ASP.NET são assíncronas. Consulte [programação assíncrona com Async e Await](https://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código no exemplo a seguir pressupõe que você está usando métodos de programação assíncronas.
> 
> 

Para criar um contêiner em sua conta de armazenamento, tudo o que você precisa fazer é adicionar uma chamada para **CreateIfNotExistsAsync** como no código a seguir:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Para disponibilizar os arquivos dentro do contêiner para todos, você pode definir o contêiner como público usando o código a seguir.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Qualquer pessoa na Internet pode ver BLOBs em um contêiner público, mas você pode modificá-los ou excluí-los somente se tiver a chave de acesso apropriada.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O armazenamento do Azure dá suporte a blobs de blocos e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o método **UploadFromStream**. Esta operação cria o blob caso não exista, ou substitui se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **ListBlobs** do contentor para obter os blobs e/ou os diretórios dentro do mesmo. Para acessar o rico conjunto de propriedades e métodos para um **IListBlobItem**retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor **photos**:

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

Conforme mostrado no exemplo de código anterior, o serviço blob também tem o conceito de diretórios dentro de contêineres. Isso é para que você possa organizar seus BLOBs em uma estrutura mais semelhante a pastas. Por exemplo, considere o seguinte conjunto de blobs de blocos num contentor com o nome **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando você chama **ListBlobs** no contêiner (como no exemplo anterior), a coleção retornada contém objetos **CloudBlobDirectory** e **CloudBlockBlob** que representam os diretórios e blobs contidos no nível superior. Aqui está a saída resultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método**ListBlobs** como **true**. Isso resulta em cada blob retornado como um **CloudBlockBlob**, independentemente do diretório. Aqui está a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e aqui estão os resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer. ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Transferir blobs
Para transferir blobs, obtenha primeiro uma referência de blob e, em seguida, chame o método **DownloadToStream**. O exemplo seguinte utiliza o método **DownloadToStream** para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Também pode utilizar o método **DownloadToStream** para transferir os conteúdos de um blob como uma cadeia de texto.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar blobs
Para excluir um blob, primeiro obtenha uma referência de BLOB e, em seguida, chame o método **delete** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas de forma assíncrona
Se está a listar um grande número de blobs ou pretende controlar o número de resultados devolvido numa operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como devolver resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera por devolver um grande conjunto de resultados.

Este exemplo mostra uma lista de blobs não hierárquica, mas também pode criar uma lista hierárquica ao definir o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** como **falso**.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido pela palavra-chave **async** e tem de devolver um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de exemplo até que a tarefa de listagem seja concluída.

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

