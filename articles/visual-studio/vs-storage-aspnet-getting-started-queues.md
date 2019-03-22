---
title: Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET) | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de filas do Azure num projeto do ASP.NET no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligado Services
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 3f3a649308449a8ba4e1dbaebd4db4afbf731c5b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105765"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de filas do Azure fornece mensagens entre componentes da aplicação na cloud. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Este tutorial mostra como escrever código do ASP.NET para alguns cenários comuns com entidades de armazenamento de filas do Azure. Estes cenários incluem tarefas comuns, como criar uma fila do Azure e adicionar, modificar, ler e remover mensagens na fila.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. Na **Explorador de soluções**, clique com botão direito **controladores**e, no menu de contexto, selecione **adicionar -> controlador**.

    ![Adicionar um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Sobre o **Adicionar estrutura** caixa de diálogo, selecione **controlador MVC 5 – vazio**e selecione **Add**.

    ![Especifique o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Sobre o **Add Controller** caixa de diálogo, o nome do controlador *QueuesController*e selecione **adicionar**.

    ![Nome do controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adicione as seguintes *usando* diretivas para o `QueuesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Criar uma fila

Os passos seguintes mostram como criar uma fila:

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`. 

1. Adicionar um método chamado **CreateQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro de **CreateQueue** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obter um **CloudQueue** objeto que representa uma referência para o nome da fila desejada. O **CloudQueueClient.GetQueueReference** método não faz um pedido com o armazenamento de filas. A referência é devolvida se a fila existe ou não. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.CreateIfNotExists** método para criar a fila se ainda não existir. O **CloudQueue.CreateIfNotExists** retorno do método **verdadeiro** se a fila não existe e é criada com êxito. Caso contrário, **false** é devolvido.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Atualização do **ViewBag** com o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **CreateQueue** para o nome da vista, selecione **Add**.

1. Abra `CreateQueue.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecione **criar fila** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Criar fila](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Como mencionado anteriormente, o **CloudQueue.CreateIfNotExists** retorno do método **verdadeiro** apenas quando a fila não existe e é criada. Por conseguinte, se executar a aplicação quando a fila existe, o método retorna **false**. Para executar a aplicação com várias vezes, tem de eliminar a fila antes de executar a aplicação novamente. A eliminar a fila pode ser feita através da **CloudQueue.Delete** método. Também pode eliminar a fila utilizando o [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem numa fila

Assim que tiver [criar uma fila](#create-a-queue), pode adicionar mensagens para essa fila. Esta secção explica como adicionar uma mensagem numa fila *teste fila*. 

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicionar um método chamado **AddMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro de **AddMessage** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Criar a **CloudQueueMessage** objeto que representa a mensagem que pretende adicionar à fila. R **CloudQueueMessage** objeto pode ser criado a partir de uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chamar o **CloudQueue.AddMessage** método para adicionar o messaged para a fila.

    ```csharp
    queue.AddMessage(message);
    ```

1. Crie e defina alguns **ViewBag** propriedades para apresentar na vista.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **AddMessage** para o nome da vista, selecione **Add**.

1. Abra `AddMessage.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **Adicionar mensagem** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Adicionar mensagem](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

As duas secções - [ler uma mensagem de uma fila sem a remover](#read-a-message-from-a-queue-without-removing-it) e [leitura e remover uma mensagem de uma fila](#read-and-remove-a-message-from-a-queue) -mostram como ler mensagens de uma fila.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Ler uma mensagem de uma fila sem a remover

Esta secção ilustra como uma olhada numa mensagem em fila (ler a primeira mensagem sem a remover).  

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicionar um método chamado **PeekMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro de **PeekMessage** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.PeekMessage** método para ler a primeira mensagem na fila sem a remover da fila. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Atualização do **ViewBag** com dois valores: o nome da fila e a mensagem que foram lidos. O **CloudQueueMessage** objeto expõe duas propriedades para obter o valor do objeto: **CloudQueueMessage.AsBytes** e **CloudQueueMessage.AsString**. **AsString** (utilizada neste exemplo) retorna uma cadeia de caracteres, enquanto **AsBytes** retorna uma matriz de bytes.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **PeekMessage** para o nome da vista, selecione **Add**.

1. Abra `PeekMessage.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **Peek message** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Observar mensagem](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Ler e remover uma mensagem de uma fila

Nesta secção, saiba como ler e remover uma mensagem de uma fila.   

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicionar um método chamado **ReadMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro de **ReadMessage** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.GetMessage** método para ler a primeira mensagem na fila. O **CloudQueue.GetMessage** método torna a mensagem invisível durante 30 segundos (por predefinição) para qualquer outro código de leitura de mensagens, para que nenhum outro código pode modificar ou eliminar a mensagem ao seu processá-lo. Para alterar a quantidade de tempo em que a mensagem permanece invisível, modifique o **visibilityTimeout** parâmetro a ser passado para o **CloudQueue.GetMessage** método.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chamar o **CloudQueueMessage.Delete** método para eliminar a mensagem da fila.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Atualização do **ViewBag** com a mensagem eliminado e o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **ReadMessage** para o nome da vista, selecione **Add**.

1. Abra `ReadMessage.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **ler/Eliminar mensagem** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Ler e eliminar mensagem](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Esta secção ilustra como obter o comprimento da fila (número de mensagens). 

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicionar um método chamado **GetQueueLength** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro de **ReadMessage** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.FetchAttributes** método para recuperar os atributos da fila (incluindo seu comprimento). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Acesso a **CloudQueue.ApproximateMessageCount** propriedade para obter o comprimento da fila.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Atualização do **ViewBag** com o nome da fila e seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **GetQueueLength** para o nome da vista, selecione **Add**.

1. Abra `GetQueueLengthMessage.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Executar a aplicação e selecione **obter o comprimento da fila** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Obter o comprimento da fila](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminar uma fila
Esta secção ilustra como eliminar uma fila. 

> [!NOTE]
> 
> Esta secção assume que concluiu os passos [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicionar um método chamado **DeleteQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro de **DeleteQueue** método, obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure: (Alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.Delete** método para eliminar a fila representada pela **CloudQueue** objeto.

    ```csharp
    queue.Delete();
    ```

1. Atualização do **ViewBag** com o nome da fila e seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Na **Explorador de soluções**, expanda o **vistas** pasta, com o botão direito **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. Sobre o **Adicionar vista** caixa de diálogo, introduza **DeleteQueue** para o nome da vista, selecione **Add**.

1. Abra `DeleteQueue.cshtml`e modificá-lo para que ele se parece com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Na **Explorador de soluções**, expanda o **vistas -> partilhado** e, abra `_Layout.cshtml`.

1. Após a última **ActionLink**, adicione as seguintes **ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecione **obter o comprimento da fila** para ver os resultados semelhantes à seguinte captura de ecrã:
  
    ![Eliminar fila](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Passos Seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento de Blobs do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao armazenamento de tabelas do Azure e o Visual Studio ligado serviços (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
