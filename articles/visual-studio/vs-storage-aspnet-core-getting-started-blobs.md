---
title: Introdução ao Blob do Azure (ASP.NET Core) de serviços de ligados de armazenamento e o Visual Studio | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de Blobs do Azure num projeto ASP.NET Core no Visual Studio, depois de ligar a uma conta de armazenamento com o Visual Studio serviços ligados
services: storage
documentationcenter: ''
author: camsoper
manager: wpickett
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: f8de7f7a263a087abb16ed48d05b2cae9834b4ad
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110519"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Azure Blob storage e o Visual Studio ligados a serviços (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [Núcleo do ASP.NET](./vs-storage-aspnet-core-getting-started-blobs.md)

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na cloud como objetos ou os blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. Para saber mais sobre o armazenamento de BLOBs, veja [introdução ao armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md).

Este tutorial mostra como escrever código do ASP.NET Core para alguns cenários comuns que utilizam o armazenamento de Blobs. Os cenários incluem criar um contentor de BLOBs, carregar, listagem, transferir e eliminar blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta secção descreve como configurar o ambiente de desenvolvimento. Isto inclui a criação de uma aplicação ASP.NET Model-View-Controller (MVC), adicionar uma ligação de serviços ligados, adicionar um controlador e especificando as diretivas de espaço de nomes necessários.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um projeto de aplicação ASP.NET MVC

1. Abra o Visual Studio.

1. A partir do menu principal, selecione **arquivo** > **New** > **projeto**.

1. Na **novo projeto** caixa de diálogo, selecione **Web** > **aplicação Web ASP.NET Core** > **AspNetCoreStorage**. Em seguida, selecione **OK**.

    ![Caixa de diálogo de captura de ecrã do novo projeto do Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Na **novo aplicativo da Web ASP.NET Core** caixa de diálogo, selecione **.NET Core** > **ASP.NET Core 2.0** > **(aplicação Web Model-View-Controller)**. Em seguida, selecione **OK**.

    ![Caixa de diálogo de captura de ecrã da nova aplicação Web ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilizar serviços ligados para ligar a uma conta de armazenamento do Azure

1. Na **Explorador de soluções**, com o botão direito no projeto.

2. No menu de contexto, selecione **Add** > **serviço ligado**.

1. Na **serviços ligados** caixa de diálogo, selecione **armazenamento na Cloud com o armazenamento do Azure**e, em seguida, selecione **configurar**.

    ![Caixa de diálogo de captura de ecrã de serviços ligados](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Na **armazenamento do Azure** caixa de diálogo, selecione o armazenamento do Azure da conta a ser utilizado para este tutorial. Para criar uma nova conta de armazenamento do Azure, selecione **criar uma nova conta de armazenamento**e preencha o formulário. Depois de selecionar a conta de armazenamento existente ou criar uma nova, selecione **adicionar**. Visual Studio instala o pacote NuGet para o armazenamento do Azure e uma cadeia de ligação de armazenamento para **appSettings**.

> [!TIP]
> Para saber como criar uma conta de armazenamento com o [portal do Azure](https://portal.azure.com), consulte [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
>
> Também pode criar uma conta de armazenamento, utilizando [do Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [CLI do Azure](../storage/common/storage-azure-cli.md), ou [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. Na **Explorador de soluções**, clique com botão direito **controladores**.

2. No menu de contexto, selecione **Add** > **controlador**.

    ![Captura de ecrã do Explorador de soluções](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Na **Adicionar estrutura** caixa de diálogo, selecione **controlador MVC - vazia**e selecione **Add**.

    ![Caixa de diálogo Adicionar captura de ecrã de estrutura](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Na **Adicionar controlador MVC vazio** caixa de diálogo, o nome do controlador *BlobsController*e selecione **Add**.

    ![Caixa de diálogo de captura de ecrã de adicionar vazio controlador MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adicione as seguintes `using` diretivas para o `BlobsController.cs` ficheiro:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Ligar a uma conta de armazenamento e obter uma referência de contentor

Um contentor de BLOBs é uma hierarquia aninhada de blobs e pastas. O resto dos passos neste documento exigem uma referência a um contentor de BLOBs, para que o código deve ser colocado em seu próprio método para reutilização.

Os passos seguintes criam um método para ligar à conta de armazenamento com a cadeia de ligação no **appSettings**. Os passos também criam uma referência a um contentor. A definição da cadeia de ligação no **appSettings** com o nome com o formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicionar um método chamado **GetCloudBlobContainer** que retorna um **CloudBlobContainer**. Não se esqueça de substituir `<storageaccountname>_AzureStorageConnectionString` com o nome real da chave no **Web. config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Embora *contentor de BLOBs de teste* não existe ainda, este código cria uma referência a ele. Isto é, para o contentor pode ser criado com o `CreateIfNotExists` método mostrado no próximo passo.

## <a name="create-a-blob-container"></a>Criar um contentor de blobs

Os passos seguintes mostram como criar um contentor de BLOBs:

1. Adicionar um método chamado `CreateBlobContainer` que retorna um `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Obter um `CloudBlobContainer` objeto que representa uma referência ao nome do contentor de BLOBs pretendido. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Chamar o `CloudBlobContainer.CreateIfNotExists` método para criar o contentor, se ainda não existir. O `CloudBlobContainer.CreateIfNotExists` retorno do método **true** se o contentor não existe e é criado com êxito. Caso contrário, o método retorna **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Atualização `ViewBag` com o nome do contentor de Blobs.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    O seguinte mostra o concluído `CreateBlobContainer` método:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Na **Explorador de soluções**, clique com botão direito a **vistas** pasta.

2. No menu de contexto, selecione **Add** > **nova pasta**. Nomeie a nova pasta *Blobs*. 

1. Na **Explorador de soluções**, expanda o **vistas** pasta e o botão direito do mouse **Blobs**.

4. No menu de contexto, selecione **Add** > **vista**.

1. Na **Adicionar vista** caixa de diálogo, introduza **CreateBlobContainer** para o nome da vista, selecione **Add**.

1. Abra `CreateBlobContainer.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Na **Explorador de soluções**, expanda o **vistas** > **partilhado** e, abra `_Layout.cshtml`.

1. Procure a lista não ordenada que tem esta aparência: `<ul class="nav navbar-nav">`.  Após a última `<li>` elemento na lista, adicione o seguinte HTML para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Executar a aplicação e selecione **criar contentor de BLOBs** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Captura de ecrã de criar o contentor de BLOBs](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Como mencionado anteriormente, o `CloudBlobContainer.CreateIfNotExists` retorno do método **true** apenas quando o contentor não existe e é criado. Por conseguinte, se a aplicação é executada quando o contentor existe, o método retorna **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Carregar um blob para um contentor de BLOBs

Quando o [contentor de BLOBs é criado](#create-a-blob-container), carregar ficheiros para esse contentor. Esta secção explica-carregar um ficheiro local para um contentor de Blobs. Os passos partem do princípio de um contentor de Blobs com o nome não existe *contentor de BLOBs de teste*. 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicionar um método chamado `UploadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro de `UploadBlob` método, obter um `CloudBlobContainer` objeto que representa uma referência ao nome do contentor de BLOBs pretendido. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. O armazenamento do Azure suporta tipos de blob diferente. Este tutorial utiliza os blobs de blocos. Para obter uma referência a um blob de blocos, chame o `CloudBlobContainer.GetBlockBlobReference` método.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > O nome do blob é parte do URL usada para recuperar um blob e pode ser qualquer cadeia de caracteres, incluindo o nome do ficheiro.

1. Após uma referência de blob, pode carregar qualquer fluxo de dados para o mesmo ao chamar o objeto de referência de blob `UploadFromStream` método. O `UploadFromStream` método cria o blob, caso ele não existe, ou substitui se existir. (Alteração  *&lt;carregamento do ficheiro >* para um caminho totalmente qualificado para um ficheiro a ser carregado.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    O seguinte mostra o concluído `UploadBlob` método (com um caminho totalmente qualificado para o ficheiro a carregar):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Na **Explorador de soluções**, expanda o **vistas** > **partilhado** e, abra `_Layout.cshtml`.

1. Após a última `<li>` elemento na lista, adicione o seguinte HTML para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Executar a aplicação e selecione **carregar blob**. A palavra *sucesso!* deve aparecer.
    
    ![Captura de ecrã da verificação de sucesso](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Listar os blobs num contentor de BLOBs

Esta secção ilustra como listar os blobs num contentor de Blobs. As referências de código de exemplo da *contentor de BLOBs de teste* criada na secção, [criar um contentor de BLOBs](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicionar um método chamado `ListBlobs` que retorna um `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Dentro de `ListBlobs` método, obter um `CloudBlobContainer` objeto que representa uma referência para o contentor de Blobs. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Para listar os blobs num contentor de BLOBs, utilize o `CloudBlobContainer.ListBlobsSegmentedAsync` método. O `CloudBlobContainer.ListBlobsSegmentedAsync` método devolve um `BlobResultSegment`. Ele contém `IListBlobItem` objetos que podem ser convertidos no `CloudBlockBlob`, `CloudPageBlob`, ou `CloudBlobDirectory` objetos. O fragmento de código seguinte enumera todos os blobs num contentor de Blobs. Cada blob é convertida para o objeto adequado, com base em seu tipo. O nome (ou URI no caso de um `CloudBlobDirectory`) é adicionado a uma lista.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    O seguinte mostra o concluído `ListBlobs` método:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. Na **Explorador de soluções**, expanda o **vistas** pasta e o botão direito do mouse **Blobs**.

2. No menu de contexto, selecione **Add** > **vista**.

1. Na **Adicionar vista** caixa de diálogo, introduza `ListBlobs` para o nome da vista, selecione **Add**.

1. Abra `ListBlobs.cshtml`e substitua os conteúdos com o código a seguir:

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

1. Na **Explorador de soluções**, expanda o **vistas** > **partilhado** e, abra `_Layout.cshtml`.

1. Após a última `<li>` elemento na lista, adicione o seguinte HTML para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Executar a aplicação e selecione **listar blobs** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Captura de ecrã de listar os blobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Transferir blobs

Esta secção ilustra como transferir um blob. Pode mantê-lo no armazenamento local ou ler o conteúdo numa cadeia de caracteres. As referências de código de exemplo da *contentor de BLOBs de teste* criada na secção, [criar um contentor de BLOBs](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicionar um método chamado `DownloadBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro de `DownloadBlob` método, obter um `CloudBlobContainer` objeto que representa uma referência para o contentor de Blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob ao chamar o `CloudBlobContainer.GetBlockBlobReference` método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para transferir um blob, utilize o `CloudBlockBlob.DownloadToStream` método. O código seguinte transfere o conteúdo de um blob para um objeto de fluxo. Esse objeto, em seguida, é mantido num arquivo local. (Alteração  *&lt;o nome de ficheiro local >* para o nome de ficheiro completamente qualificado que representa em que o blob é para ser transferido.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    O seguinte mostra o concluído `ListBlobs` método (com um caminho totalmente qualificado para o ficheiro local que está a ser criado):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Na **Explorador de soluções**, expanda o **vistas** > **partilhado** e, abra `_Layout.cshtml`.

1. Após a última `<li>` elemento na lista, adicione o seguinte HTML para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Executar a aplicação e selecione **Download blob** para transferir o blob. O blob especificado na `CloudBlobContainer.GetBlockBlobReference` downloads de chamada de método para a localização especificada no `File.OpenWrite` chamada de método. O texto *sucesso!* deve aparecer no navegador. 

## <a name="delete-blobs"></a>Eliminar blobs

Os passos seguintes mostram como eliminar um blob:

1. Abra o ficheiro `BlobsController.cs`.

1. Adicionar um método chamado `DeleteBlob` que retorna uma cadeia de caracteres.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Dentro de `DeleteBlob` método, obter um `CloudBlobContainer` objeto que representa uma referência para o contentor de Blobs.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência de blob ao chamar o `CloudBlobContainer.GetBlockBlobReference` método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para eliminar um blob, utilize o `Delete` método.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    O concluído `DeleteBlob` método deverá aparecer da seguinte forma:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. Na **Explorador de soluções**, expanda o **vistas** > **partilhado** e, abra `_Layout.cshtml`.

1. Após a última `<li>` elemento na lista, adicione o seguinte HTML para adicionar outro item de menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Executar a aplicação e selecione **eliminação de BLOBs** para eliminar o blob especificado no `CloudBlobContainer.GetBlockBlobReference` chamada de método. O texto *sucesso!* deve aparecer no navegador. Selecione o browser **volta** e, em seguida, selecione **listar blobs** para verificar se o blob já não está no contentor.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como armazenar, listar e obter os blobs no armazenamento do Azure utilizando o ASP.NET Core. Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao Azure Table storage e o Visual Studio ligados a serviços (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introdução à fila do Azure (ASP.NET) de serviços de ligado de armazenamento e o Visual Studio](vs-storage-aspnet-getting-started-queues.md)
