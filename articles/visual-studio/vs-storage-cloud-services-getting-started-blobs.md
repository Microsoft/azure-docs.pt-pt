---
title: Introdução ao blob storage e o Visual Studio ligado services (serviços cloud) | Documentos da Microsoft
description: Serviços ligados de como começar a utilizar o armazenamento de Blobs do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 9f1ef06e0275954343c548d0f6937b9c6fbcfd18
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012128"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de Blobs do Azure e o Visual Studio serviços ligados (projetos de serviços cloud)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar com o armazenamento de Blobs do Azure depois de criado ou referenciados uma conta de armazenamento do Azure utilizando o Visual Studio **adicionar serviços ligados** projeto dos serviços de caixa de diálogo numa nuvem do Visual Studio. Mostraremos como aceder e criar contentores de BLOBs e como executar tarefas comuns, como carregamento, a listagem e transferir blobs. Os exemplos são escritos em C\# e utilizar o [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, que podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Um blob único pode ser qualquer tamanho. BLOBs podem ser coisas como imagens, arquivos de áudio e vídeo, dados não processados e arquivos do documento.

Assim como ficheiros residam em pastas, os blobs de armazenamento em direto em contentores. Depois de criar um armazenamento, criar um ou mais contentores no armazenamento. Por exemplo, num armazenamento chamado "Scrapbook", pode criar contentores no armazenamento chamado "imagens" para armazenar imagens e outro chamado "áudio" para armazenar os arquivos de áudio. Depois de criar os contentores, pode carregar ficheiros de blob individuais aos mesmos.

* Para obter mais informações sobre a manipulação programática de blobs, veja [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Para obter informações gerais sobre o armazenamento do Azure, consulte [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/).
* Para obter informações gerais sobre o Azure Cloud Services, consulte [documentação de serviços Cloud](https://azure.microsoft.com/documentation/services/cloud-services/).
* Para obter mais informações sobre a programação de aplicativos do ASP.NET, consulte [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contentores de BLOBs de acesso no código
Para acessar programaticamente os blobs em projetos de serviço cloud, terá de adicionar os seguintes itens, se não ainda estiverem presentes.

1. Adicione as seguintes declarações de espaço de nomes de código na parte superior de qualquer arquivo do c# no qual pretende aceder programaticamente ao armazenamento do Azure.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Obter um **CloudBlobClient** objeto para fazer referência a um contentor existente na sua conta de armazenamento.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Obter um **CloudBlobContainer** objeto para fazer referência a um contentor de blob específico.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Utilize todo o código mostrado no procedimento anterior à frente o código mostrado nas seções a seguir.
> 
> 

## <a name="create-a-container-in-code"></a>Criar um contentor no código
> [!NOTE]
> Algumas APIs que realizam chamadas horizontalmente ao armazenamento do Azure no ASP.NET são assíncronas. Ver [programação assíncrona com Async e Await](https://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código no exemplo a seguir pressupõe que está a utilizar métodos de programação assíncrona.
> 
> 

Para criar um contentor na conta de armazenamento, tudo o que precisa fazer é adicionar uma chamada para **CreateIfNotExistsAsync** como no seguinte código:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Para disponibilizar a todos os ficheiros dentro do contêiner, pode definir o contentor como público utilizando o código a seguir.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Qualquer pessoa na Internet pode ver os blobs num contentor público, mas pode modificar ou eliminá-los apenas se tiver a chave de acesso adequada.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O armazenamento do Azure suporta blobs de blocos e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o método **UploadFromStream**. Esta operação cria o blob caso não exista, ou substitui se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **ListBlobs** do contentor para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **IListBlobItem**, tem de o transmitir para um **CloudBlockBlob**, **CloudPageBlob**, ou  **CloudBlobDirectory** objeto. Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor **photos**:

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

Conforme mostrado no exemplo de código anterior, o serviço de BLOBs tem o conceito de diretórios dentro de contentores, também. Isto é, de modo que pode organizar os blobs numa estrutura mais semelhantes a pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos num contentor com o nome **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando chama **ListBlobs** no contentor (como no exemplo anterior), contém a coleção retornada **CloudBlobDirectory** e **CloudBlockBlob** objetos que representam os diretórios e blobs contidos no nível superior. Eis a saída resultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método**ListBlobs** como **true**. Isso resulta em todos os BLOBs que está a ser retornado como um **CloudBlockBlob**, independentemente do diretório. Segue-se a chamada para **ListBlobs**:

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

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

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
Para eliminar um blob, obtenha primeiro uma referência de blob e, em seguida, chame o **eliminar** método.

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

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

