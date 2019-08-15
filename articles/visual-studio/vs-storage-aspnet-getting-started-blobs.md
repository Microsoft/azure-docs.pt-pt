---
title: Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (ASP.NET) | Microsoft Docs
description: Como começar a usar o armazenamento de BLOBs do Azure em um projeto ASP.NET no Visual Studio, depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 229522d16db0571c7dbe50e05fceaca9491c4c58
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034158"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [Núcleo do ASP.NET](./vs-storage-aspnet-core-getting-started-blobs.md)

O armazenamento de BLOBs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos ou BLOBs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. Para saber mais sobre o armazenamento de BLOBs, consulte [introdução ao armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md).

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns que usam o armazenamento de BLOBs. Os cenários incluem a criação de um contêiner de BLOBs e o carregamento, a listagem, o download e a exclusão de BLOBs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **controladores**.

2. No menu de contexto, selecione **Adicionar** > **controlador**.

    ![Captura de tela de Gerenciador de Soluções, com adicionar e controlador realçado](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold** , selecione **controlador MVC 5 – vazio**e selecione **Adicionar**.

    ![Captura de tela da caixa de diálogo Adicionar Scaffold](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Na caixa de diálogo **Adicionar controlador** , nomeie o controlador *BlobsController*e selecione **Adicionar**.

    ![Captura de tela da caixa de diálogo Adicionar controlador](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adicione as seguintes `using` diretivas `BlobsController.cs` ao arquivo:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Conectar-se a uma conta de armazenamento e obter uma referência de contêiner

Um contêiner de blob é uma hierarquia aninhada de BLOBs e pastas. O restante das etapas neste documento requer uma referência a um contêiner de BLOBs, de modo que o código deve ser colocado em seu próprio método para reutilização.

As etapas a seguir criam um método para se conectar à conta de armazenamento usando a cadeia de conexão em **Web. config**. As etapas também criam uma referência a um contêiner.  A configuração da cadeia de conexão em **Web. config** é nomeada com `<storageaccountname>_AzureStorageConnectionString`o formato. 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado **GetCloudBlobContainer** que retorna um **CloudBlobContainer**.  Certifique-se de `<storageaccountname>_AzureStorageConnectionString` substituir pelo nome real da chave em **Web. config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Embora *Test-blob-container* ainda não exista, esse código cria uma referência a ele. Isso é para que o contêiner possa ser criado com `CreateIfNotExists` o método mostrado na próxima etapa.

## <a name="create-a-blob-container"></a>Criar um contentor de blobs

As etapas a seguir ilustram como criar um contêiner de BLOBs:

1. Adicione um método chamado `CreateBlobContainer` que retorna um `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Obtenha um `CloudBlobContainer` objeto que representa uma referência ao nome do contêiner de blob desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Chame o `CloudBlobContainer.CreateIfNotExists` método para criar o contêiner, se ele ainda não existir. O `CloudBlobContainer.CreateIfNotExists` método retornará **true** se o contêiner não existir e for criado com êxito. Caso contrário, o método retornará **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Atualize `ViewBag` com o nome do contêiner de BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    O seguinte mostra o método `CreateBlobContainer` concluído:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta exibições.

1. Se não houver uma pasta BLOBs, crie uma. No menu de contexto, selecione **Adicionar** > **nova pasta**. Nomeie os novos *BLOBs*de pastas. 
 
1. Em **Gerenciador de soluções**, expanda a pasta exibições e clique com o botão direito do mouse em **BLOBs**.

1. No menu de contexto, selecione **Adicionar** > **exibição**.

1. Na caixa de diálogo **Adicionar exibição** , digite **CreateBlobContainer** para o nome da exibição e selecione **Adicionar**.

1. Abra `CreateBlobContainer.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Em **Gerenciador de soluções**, expanda > a pasta**compartilhada** exibições e `_Layout.cshtml`abra.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **criar contêiner de blob** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Captura de tela de criar contêiner de BLOB](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Como mencionado anteriormente, o `CloudBlobContainer.CreateIfNotExists` método retorna **true** somente quando o contêiner não existe e é criado. Portanto, se o aplicativo for executado quando o contêiner existir, o método retornará **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Carregar um blob em um contêiner de BLOBs

Quando o [contêiner de blob é criado](#create-a-blob-container), carregue os arquivos nesse contêiner. Esta seção orienta o carregamento de um arquivo local para um contêiner de BLOB. As etapas pressupõem que há um contêiner de blob chamado *Test-blob-container*. 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado `UploadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro do `UploadBlob` método, obtenha um `CloudBlobContainer` objeto que representa uma referência ao nome do contêiner de blob desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. O armazenamento do Azure dá suporte a diferentes tipos de BLOB. Este tutorial usa blobs de bloco. Para recuperar uma referência a um blob de blocos, chame `CloudBlobContainer.GetBlockBlobReference` o método.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > O nome do blob é parte da URL usada para recuperar um blob e pode ser qualquer cadeia de caracteres, incluindo o nome do arquivo.

1. Depois de haver uma referência de BLOB, você pode carregar qualquer fluxo de dados para ele chamando o método do objeto `UploadFromStream` de referência de BLOB. O `UploadFromStream` método criará o blob, se ele não existir, ou o substituirá se ele existir. (Altere  *&lt;o arquivo para carregar >* para um caminho totalmente qualificado para um arquivo a ser carregado.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    O seguinte mostra o método `UploadBlob` concluído (com um caminho totalmente qualificado para o arquivo a ser carregado):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. Em **Gerenciador de soluções**, expanda > a pasta**compartilhada** exibições e `_Layout.cshtml`abra.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **carregar blob**.  A palavra *sucesso!* deve aparecer.
    
    ![Captura de tela de verificação de êxito](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Listar os BLOBs em um contêiner de BLOBs

Esta seção ilustra como listar os BLOBs em um contêiner de BLOBs. O código de exemplo faz referência ao *contêiner Test-blob-* criado na seção [criar um contêiner de blob](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado `ListBlobs` que retorna um `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Dentro do `ListBlobs` método, obtenha um `CloudBlobContainer` objeto que representa uma referência ao contêiner de BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Para listar os BLOBs em um contêiner de BLOB, `CloudBlobContainer.ListBlobs` use o método. O `CloudBlobContainer.ListBlobs` método retorna um `IListBlobItem` objeto que pode ser convertido em um `CloudBlockBlob`objeto `CloudPageBlob`, ou `CloudBlobDirectory` . O trecho de código a seguir enumera todos os BLOBs em um contêiner de BLOB. Cada blob é convertido no objeto apropriado, com base em seu tipo. Seu nome (ou URI no caso de um **CloudBlobDirectory**) é adicionado a uma lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Além dos BLOBs, os contêineres de blob podem conter diretórios. Suponha que haja um contêiner de blob chamado *Test-blob-container*, com a seguinte hierarquia:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Usando o exemplo de código anterior, a lista de cadeias de caracteres de BLOBs contém valores semelhantes aos seguintes:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Como mostrado, a lista inclui apenas as entidades de nível superior, não as aninhadas (*bar. png* e *Baz. png*). Para listar todas as entidades em um contêiner de BLOB, altere o código para que o método **CloudBlobContainer. ListBlobs** seja passado como **true** para o parâmetro **useFlatBlobListing** .    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Definir o parâmetro **useFlatBlobListing** como **true** retorna uma lista simples de todas as entidades no contêiner de BLOB. Isso produz os seguintes resultados:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    O seguinte mostra o método **ListBlobs** concluído:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Em **Gerenciador de soluções**, expanda a pasta exibições e clique com o botão direito do mouse em **BLOBs**.

2. No menu de contexto, selecione **Adicionar** > **exibição**.

1. Na caixa de diálogo **Adicionar exibição** , insira `ListBlobs` para o nome da exibição e selecione **Adicionar**.

1. Abra `ListBlobs.cshtml`e substitua o conteúdo pelo código a seguir:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. Em **Gerenciador de soluções**, expanda > a pasta**compartilhada** exibições e `_Layout.cshtml`abra.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **listar BLOBs** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Captura de tela da lista de BLOBs](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Transferir blobs

Esta seção ilustra como baixar um blob. Você pode mantê-lo no armazenamento local ou ler o conteúdo em uma cadeia de caracteres. O código de exemplo faz referência ao *contêiner Test-blob-* criado na seção [criar um contêiner de blob](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado `DownloadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro do `DownloadBlob` método, obtenha um `CloudBlobContainer` objeto que representa uma referência ao contêiner de BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob chamando `CloudBlobContainer.GetBlockBlobReference` o método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para baixar um blob, use o `CloudBlockBlob.DownloadToStream` método. O código a seguir transfere o conteúdo de um blob para um objeto de fluxo. Esse objeto é persistido em um arquivo local. (Altere  *&lt;o nome do arquivo local >* para o nome de arquivo totalmente qualificado que representa onde o blob será baixado.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    O seguinte mostra o método `DownloadBlob` concluído (com um caminho totalmente qualificado para o arquivo local que está sendo criado):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. Em **Gerenciador de soluções**, expanda > a pasta**compartilhada** exibições e `_Layout.cshtml`abra.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **baixar blob** para baixar o blob. O blob especificado na chamada `CloudBlobContainer.GetBlockBlobReference` do método é baixado para o local especificado na `File.OpenWrite` chamada do método.  O texto *sucesso!* deve aparecer no navegador. 

## <a name="delete-blobs"></a>Eliminar blobs

As etapas a seguir ilustram como excluir um blob:

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado `DeleteBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Dentro do `DeleteBlob` método, obtenha um `CloudBlobContainer` objeto que representa uma referência ao contêiner de BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob chamando `CloudBlobContainer.GetBlockBlobReference` o método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para excluir um blob, use o `Delete` método.

    ```csharp
    blob.Delete();
    ```
    
    O método `DeleteBlob` concluído deve aparecer da seguinte maneira:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. Em **Gerenciador de soluções**, expanda > a pasta**compartilhada** exibições e `_Layout.cshtml`abra.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **excluir blob** para excluir o blob especificado na chamada do `CloudBlobContainer.GetBlockBlobReference` método. O texto *sucesso!* deve aparecer no navegador. Selecione o botão **voltar** do navegador e, em seguida, selecione **listar BLOBs** para verificar se o BLOB não está mais no contêiner.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a armazenar, listar e recuperar blobs no armazenamento do Azure usando o ASP.NET. Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento de tabelas do Azure e aos serviços conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
