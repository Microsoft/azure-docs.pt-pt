---
title: Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (ASP.NET) | Microsoft Docs
description: Como começar a usar o armazenamento de filas do Azure em um projeto ASP.NET no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 19cf2dd912968d0a5df8743c1e720776b8a949f0
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515981"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

O armazenamento de filas do Azure fornece mensagens de nuvem entre componentes do aplicativo. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns usando entidades de armazenamento de filas do Azure. Esses cenários incluem tarefas comuns, como a criação de uma fila do Azure e a adição, modificação, leitura e remoção de mensagens da fila.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de soluções**, clique com o botão direito do mouse em **controladores**e, no menu de contexto, selecione **Adicionar-> controlador**.

    ![Adicionar um controlador a um aplicativo MVC ASP.NET](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold** , selecione **controlador MVC 5 – vazio**e selecione **Adicionar**.

    ![Especificar tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na caixa de diálogo **Adicionar controlador** , nomeie o controlador *QueuesController*e selecione **Adicionar**.

    ![Nomear o controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adicione as seguintes diretivas *using* ao `QueuesController.cs` arquivo:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Criar uma fila

As etapas a seguir ilustram como criar uma fila:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`. 

1. Adicione um método chamado **CreateQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateQueue** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenha um objeto **CloudQueue** que representa uma referência ao nome de fila desejado. O método **CloudQueueClient. GetQueueReference** não faz uma solicitação no armazenamento de filas. A referência é retornada se a fila existir ou não. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue. CreateIfNotExists** para criar a fila, caso ela ainda não exista. O método **CloudQueue. CreateIfNotExists** retornará **true** se a fila não existir e for criada com êxito. Caso contrário, **false** será retornado.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **CreateQueue** para o nome da exibição e selecione **Adicionar**.

1. Abra `CreateQueue.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **criar fila** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Criar fila](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Como mencionado anteriormente, o método **CloudQueue. CreateIfNotExists** retorna **true** somente quando a fila não existe e é criada. Portanto, se você executar o aplicativo quando a fila existir, o método retornará **false**. Para executar o aplicativo várias vezes, você deve excluir a fila antes de executar o aplicativo novamente. A exclusão da fila pode ser feita por meio do método **CloudQueue. Delete** . Você também pode excluir a fila usando o [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Depois de [criar uma fila](#create-a-queue), você pode adicionar mensagens a essa fila. Esta seção orienta você pela adição de uma mensagem a uma fila *Test-Queue*. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **AddMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do Método AddMessage, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Crie o objeto **CloudQueueMessage** que representa a mensagem que você deseja adicionar à fila. Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chame o método **CloudQueue. AddMessage** para adicionar a mensagem à fila.

    ```csharp
    queue.AddMessage(message);
    ```

1. Crie e defina algumas propriedades **ViewBag** para exibição na exibição.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite AddMessage para o nome da exibição e selecione **Adicionar**.

1. Abra `AddMessage.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Adicionar mensagem** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Adicionar mensagem](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

As duas seções- [lêem uma mensagem de uma fila sem removê-la](#read-a-message-from-a-queue-without-removing-it) e [ler e remover uma mensagem de uma fila](#read-and-remove-a-message-from-a-queue) -ilustram como ler mensagens de uma fila.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Ler uma mensagem de uma fila sem removê-la

Esta seção ilustra como inspecionar uma mensagem enfileirada (ler a primeira mensagem sem removê-la).  

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **PeekMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **PeekMessage** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue. PeekMessage** para ler a primeira mensagem na fila sem removê-la da fila. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Atualize a **ViewBag** com dois valores: o nome da fila e a mensagem que foi lida. O objeto **CloudQueueMessage** expõe duas propriedades para obter o valor do objeto: **CloudQueueMessage. AsBytes** e **CloudQueueMessage. AsString**. **AsString** (usado neste exemplo) retorna uma cadeia de caracteres, enquanto AsBytes retorna uma matriz de bytes.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **PeekMessage** para o nome da exibição e selecione **Adicionar**.

1. Abra `PeekMessage.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Inspecionar mensagem** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Inspecionar mensagem](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Ler e remover uma mensagem de uma fila

Nesta seção, você aprenderá a ler e remover uma mensagem de uma fila.   

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **ReadMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue. GetMessage** para ler a primeira mensagem na fila. O método **CloudQueue. GetMessage** torna a mensagem invisível por 30 segundos (por padrão) a qualquer outra mensagem de leitura de código para que nenhum outro código possa modificar ou excluir a mensagem enquanto o processa. Para alterar a quantidade de tempo em que a mensagem é invisível, modifique o parâmetro **visibilityTimeout** que está sendo passado para o método **CloudQueue. GetMessage** .

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chame o método **CloudQueueMessage. Delete** para excluir a mensagem da fila.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Atualize a **ViewBag** com a mensagem excluída e o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **ReadMessage** para o nome da exibição e selecione **Adicionar**.

1. Abra `ReadMessage.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

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

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **ler/Excluir mensagem** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Ler e excluir mensagem](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Esta seção ilustra como obter o comprimento da fila (número de mensagens). 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **GetQueueLength** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue. fetchattributes** para recuperar os atributos da fila (incluindo seu comprimento). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Acesse a propriedade **CloudQueue. ApproximateMessageCount** para obter o comprimento da fila.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Atualize o **ViewBag** com o nome da fila e seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **GetQueueLength** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetQueueLengthMessage.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **obter comprimento da fila** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Obter comprimento da fila](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminar uma fila
Esta seção ilustra como excluir uma fila. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas de [configuração do ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **DeleteQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **DeleteQueue** , obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure: (Altere  *&lt;Storage-Account-Name >* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um objeto **CloudQueueClient** representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue. Delete** para excluir a fila representada pelo objeto **CloudQueue** .

    ```csharp
    queue.Delete();
    ```

1. Atualize o **ViewBag** com o nome da fila e seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Na **Gerenciador de soluções**, expanda a pasta exibições, clique com o botão direito do mouse em **filas**e, no menu de contexto, selecione **modo de exibição de >** .

1. Na caixa de diálogo **Adicionar exibição** , digite **DeleteQueue** para o nome da exibição e selecione **Adicionar**.

1. Abra `DeleteQueue.cshtml`e modifique-o para que fique semelhante ao seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. No **Gerenciador de soluções**, expanda a pasta **exibições-> compartilhada** e abra `_Layout.cshtml`.

1. Após o último **HTML. ActionLink**, adicione o seguinte **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **obter comprimento da fila** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Eliminar fila](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Passos Seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao armazenamento de tabelas do Azure e aos serviços conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
