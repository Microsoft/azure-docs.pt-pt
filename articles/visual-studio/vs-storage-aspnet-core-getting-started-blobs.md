---
title: Inicie-se com o armazenamento Azure Blob usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento Azure Blob num projeto ASP.NET Core no Visual Studio, depois de se ligar a uma conta de armazenamento utilizando serviços conectados ao Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff221a32ff6c995d019b13f20ca2c3f9e2027f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980737"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Inicie com serviços conectados de armazenamento Azure Blob e Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

O armazenamento azure Blob é um serviço que armazena dados não estruturados na nuvem como objetos ou bolhas. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. Para saber mais sobre o armazenamento blob, consulte [Introdução ao armazenamento De Blob Azure](../storage/blobs/storage-blobs-introduction.md).

Este tutorial mostra como escrever ASP.NET código Core para alguns cenários comuns que usam o armazenamento blob. Os cenários incluem a criação de um recipiente de bolha, e o upload, listagem, descarregamento e apagando bolhas.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta secção passa pela criação do ambiente de desenvolvimento. Isto inclui a criação de uma aplicação ASP.NET Model-View-Controller (MVC), adicionando uma ligação de serviços conectados, adicionando um controlador e especificando as diretivas de espaço de nome exigidas.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um ASP.NET projeto de aplicação MVC

1. Abra o Visual Studio.

1. A partir do menu principal, selecione **File** > **New** > **Project**.

1. Na caixa de diálogo **New Project,** selecione **Web** > **ASP.NET Core Web Application** > **AspNetCoreStorage**. Em seguida, selecione **OK**.

    ![Screenshot da caixa de diálogo do estúdio visual Novo projeto](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Na caixa de diálogo **New ASP.NET Core Web Application,** selecione **.NET Core** > **ASP.NET Core 2.0** > **Web Application (Model-View-Controller)**. Em seguida, selecione **OK**.

    ![Screenshot da nova caixa de diálogo da aplicação web do núcleo de ASP.NET](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilize serviços conectados para se conectar a uma conta de armazenamento Azure

1. No **Solution Explorer,** clique no projeto.

2. A partir do menu de contexto, selecione **Adicionar** > **Serviço Conectado**.

1. Na caixa de diálogo **Connected Services,** selecione **Cloud Storage com Armazenamento Azure**, e, em seguida, selecione **Configurar**.

    ![Screenshot da caixa de diálogo de serviços conectados](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Na caixa de diálogo **Azure Storage,** selecione a conta de armazenamento Azure a utilizar para este tutorial. Para criar uma nova conta de armazenamento Azure, selecione **Criar uma Nova Conta**de Armazenamento e preencher o formulário. Depois de selecionar uma conta de armazenamento existente ou criar uma nova, **selecione Adicionar**. O Visual Studio instala o pacote NuGet para armazenamento azure, e uma cadeia de ligação de armazenamento para **appsettings.json**.

> [!TIP]
> Para aprender a criar uma conta de armazenamento com o [portal Azure,](https://portal.azure.com)consulte [Criar uma conta](../storage/common/storage-account-create.md)de armazenamento .
>
> Também pode criar uma conta de armazenamento utilizando [o Azure PowerShell,](../storage/common/storage-powershell-guide-full.md) [o Azure CLI](../storage/common/storage-azure-cli.md)ou [o Azure Cloud Shell.](../cloud-shell/overview.md)


### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Solution Explorer,** **controladores**de clique sintetizados .

2. A partir do menu de contexto, selecione **Adicionar** > **Controlador**.

    ![Screenshot do Explorador de Soluções](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Na caixa de diálogo **Add Scaffold,** selecione **Controlador MVC - Vazio**, e selecione **Adicionar**.

    ![Screenshot da caixa de diálogo add andaime](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Na caixa de diálogo **do controlador Add Empty MVC,** nomeie o controlador *BlobsController*, e selecione **Adicionar**.

    ![Screenshot de adicionar caixa de diálogo do controlador MVC vazio](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Adicione as `using` seguintes `BlobsController.cs` diretivas ao ficheiro:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Ligue-se a uma conta de armazenamento e obtenha uma referência de contentor

Um recipiente de bolhas é uma hierarquia aninhada de bolhas e pastas. O resto das etapas deste documento requerem uma referência a um recipiente de bolha, de modo a que o código seja colocado no seu próprio método de reutilização.

Os seguintes passos criam um método de ligação à conta de armazenamento utilizando a cadeia de ligação em **definições.json**. Os passos também criam uma referência a um recipiente. A definição de cordas de ligação em `<storageaccountname>_AzureStorageConnectionString` **definições.json** é nomeada com o formato . 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método chamado **GetCloudBlobContainer** que devolve um **CloudBlobContainer**. Certifique-se `<storageaccountname>_AzureStorageConnectionString` de que substitui o nome real da chave em **Web.config**.
    
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
> Mesmo que o *recipiente de teste* ainda não exista, este código cria uma referência a ele. Isto é para que o recipiente `CreateIfNotExists` possa ser criado com o método mostrado no próximo passo.

## <a name="create-a-blob-container"></a>Criar um contentor de blobs

Os seguintes passos ilustram como criar um recipiente de bolhas:

1. Adicione um `CreateBlobContainer` método chamado `ActionResult`que devolve um .

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Obtenha `CloudBlobContainer` um objeto que represente uma referência ao nome de recipiente de bolhas desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ligue `CloudBlobContainer.CreateIfNotExists` para o método para criar o recipiente, se ainda não existir. O `CloudBlobContainer.CreateIfNotExists` método retorna **verdadeiro** se o recipiente não existir, e é criado com sucesso. Caso contrário, o método devolve **falsos.**    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Atualização `ViewBag` com o nome do recipiente de bolhas.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    O seguinte mostra `CreateBlobContainer` o método completo:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. No **Solution Explorer,** clique à direita na pasta **Views.**

2. A partir do menu de contexto, selecione **Adicionar** > **Nova Pasta**. Nomeie a nova pasta *Blobs*. 

1. No **Solution Explorer,** expanda a pasta **Views** e clique à direita **em Blobs**.

4. A partir do menu de contexto, selecione **Adicionar** > **Ver**.

1. Na caixa de diálogo **Add View,** introduza o **CreateBlobContainer** para o nome da vista e selecione **Adicionar**.

1. Abra `CreateBlobContainer.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Solution Explorer,** expanda a `_Layout.cshtml`pasta **Views** > **Shared** e abre.

1. Procure a lista não ordenada que `<ul class="nav navbar-nav">`se pareça com esta: .  Depois do `<li>` último elemento da lista, adicione o seguinte HTML para adicionar outro item do menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Executar a aplicação e selecionar **Recipiente Criar Blob** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Screenshot do recipiente de bolha Criar](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Como mencionado anteriormente, o `CloudBlobContainer.CreateIfNotExists` método só retorna **verdadeiro** quando o recipiente não existe e é criado. Portanto, se a aplicação for executada quando o recipiente existe, o método devolve **falsos**.

## <a name="upload-a-blob-into-a-blob-container"></a>Faça upload de uma bolha em um recipiente de bolhas

Quando o [recipiente de bolhas for criado,](#create-a-blob-container)faça upload de ficheiros para esse recipiente. Esta secção passa por enviar um ficheiro local para um contentor de bolhas. Os passos assumem que há um recipiente de bolhas chamado *recipiente de bolhas de ensaio*. 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um `UploadBlob` método chamado que devolve uma corda.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro `UploadBlob` do método, `CloudBlobContainer` obtenha um objeto que represente uma referência ao nome de recipiente de bolhas desejado. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. O armazenamento azure suporta diferentes tipos de bolhas. Este tutorial usa bolhas de bloco. Para recuperar uma referência a uma `CloudBlobContainer.GetBlockBlobReference` bolha de bloco, ligue para o método.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > O nome blob faz parte do URL usado para recuperar uma bolha, e pode ser qualquer corda, incluindo o nome do ficheiro.

1. Depois de existir uma referência blob, pode enviar-lhe qualquer fluxo de `UploadFromStream` dados, chamando o método do objeto de referência blob. O `UploadFromStream` método cria a bolha se não existir, ou a substitui se existir. (Alterar o>de * &lt;envio de ficheiros* para um caminho totalmente qualificado para um ficheiro a ser carregado.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    O seguinte mostra `UploadBlob` o método concluído (com um caminho totalmente qualificado para o ficheiro a ser carregado):

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

1. No **Solution Explorer,** expanda a `_Layout.cshtml`pasta **Views** > **Shared** e abre.

1. Depois do `<li>` último elemento da lista, adicione o seguinte HTML para adicionar outro item do menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Executar a aplicação e selecionar **upload blob**. A palavra *sucesso!* deve aparecer.
    
    ![Screenshot da verificação de sucesso](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Liste as bolhas num recipiente de bolhas

Esta secção ilustra como listar as bolhas num recipiente de bolhas. O código da amostra refere o *recipiente de teste-blob* criado na secção, [Criar um recipiente de bolha](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um `ListBlobs` método chamado `ActionResult`que devolve um .

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Dentro `ListBlobs` do método, `CloudBlobContainer` obtenha um objeto que represente uma referência ao recipiente de bolhas. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Para enumerar as bolhas num recipiente de `CloudBlobContainer.ListBlobsSegmentedAsync` bolhas, utilize o método. O `CloudBlobContainer.ListBlobsSegmentedAsync` método `BlobResultSegment`devolve um . Isto `IListBlobItem` contém objetos que `CloudBlockBlob` `CloudPageBlob`podem `CloudBlobDirectory` ser lançados para, ou objetos. O seguinte código de corte enumera todas as bolhas num recipiente de bolhas. Cada bolha é lançada para o objeto apropriado, com base no seu tipo. O seu nome (ou URI `CloudBlobDirectory`no caso de a) é adicionado a uma lista.

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
    O seguinte mostra `ListBlobs` o método completo:

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

1. No **Solution Explorer,** expanda a pasta **Views** e clique à direita **em Blobs**.

2. A partir do menu de contexto, selecione **Adicionar** > **Ver**.

1. Na caixa de diálogo Add `ListBlobs` **View,** introduza para o nome da visualização e selecione **Adicionar**.

1. Abra, `ListBlobs.cshtml`e substitua o conteúdo pelo seguinte código:

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

1. No **Solution Explorer,** expanda a `_Layout.cshtml`pasta **Views** > **Shared** e abre.

1. Depois do `<li>` último elemento da lista, adicione o seguinte HTML para adicionar outro item do menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Executar a aplicação e selecionar bolhas de lista para ver **resultados** semelhantes aos seguintes screenshot:
  
    ![Screenshot de bolhas de lista](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Transferir blobs

Esta secção ilustra como descarregar uma bolha. Pode persistir no armazenamento local ou ler o conteúdo numa corda. O código da amostra refere o *recipiente de teste-blob* criado na secção, [Criar um recipiente de bolha](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um `DownloadBlob` método chamado que devolve uma corda.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dentro `DownloadBlob` do método, `CloudBlobContainer` obtenha um objeto que represente uma referência ao recipiente de bolhas.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência `CloudBlobContainer.GetBlockBlobReference` blob chamando o método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para descarregar uma bolha, `CloudBlockBlob.DownloadToStream` use o método. O código seguinte transfere o conteúdo de uma bolha para um objeto de fluxo. Esse objeto é então persistiu a um ficheiro local. (Alterar o * &lt;nome de ficheiro local>* para o nome de ficheiro totalmente qualificado que representa o local onde a bolha deve ser descarregada.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    O seguinte mostra `ListBlobs` o método concluído (com um caminho totalmente qualificado para o arquivo local sendo criado):
    
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

1. No **Solution Explorer,** expanda a `_Layout.cshtml`pasta **Views** > **Shared** e abre.

1. Depois do `<li>` último elemento da lista, adicione o seguinte HTML para adicionar outro item do menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Execute a aplicação e selecione **Baixar blob** para baixar a bolha. A bolha especificada `CloudBlobContainer.GetBlockBlobReference` no método de chamada descarregamentos para o local especificado na chamada do `File.OpenWrite` método. O sucesso do *texto!* deve aparecer no navegador. 

## <a name="delete-blobs"></a>Eliminar blobs

Os seguintes passos ilustram como eliminar uma bolha:

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um `DeleteBlob` método chamado que devolve uma corda.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Dentro `DeleteBlob` do método, `CloudBlobContainer` obtenha um objeto que represente uma referência ao recipiente de bolhas.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Obtenha um objeto de referência `CloudBlobContainer.GetBlockBlobReference` blob chamando o método. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Para eliminar uma bolha, `Delete` utilize o método.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    O método `DeleteBlob` completo deve figurar da seguinte forma:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. No **Solution Explorer,** expanda a `_Layout.cshtml`pasta **Views** > **Shared** e abre.

1. Depois do `<li>` último elemento da lista, adicione o seguinte HTML para adicionar outro item do menu de navegação:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Executar a aplicação e selecionar **Apagar blob** para `CloudBlobContainer.GetBlockBlobReference` eliminar a bolha especificada na chamada do método. O sucesso do *texto!* deve aparecer no navegador. Selecione o botão **back** do navegador e, em seguida, selecione **'List blobs'** para verificar se a bolha já não se encontra no recipiente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a armazenar, listar e recuperar bolhas no Armazenamento Azure usando ASP.NET Core. Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Inicie com serviços de armazenamento de mesa Azure e serviços conectados ao Estúdio Visual (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Inicie com o armazenamento de fila Azure e serviços conectados ao Estúdio Visual (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
