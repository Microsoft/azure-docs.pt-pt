---
title: Começar com o armazenamento de fila Azure usando o Visual Studio (ASP.NET)
description: Como começar a usar o armazenamento de fila Azure num projeto de ASP.NET no Estúdio Visual depois de se ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980745"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Inicie-se com o armazenamento de fila Azure e serviços visual de estúdio conectados (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

O armazenamento de fila Azure fornece mensagens em nuvem entre componentes de aplicações. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Este tutorial mostra como escrever ASP.NET código para alguns cenários comuns usando entidades de armazenamento de fila Azure. Estes cenários incluem tarefas comuns como criar uma fila Azure, e adicionar, modificar, ler e remover mensagens de fila.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento azure](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Solution Explorer,** **controladores**de clique soro direito, e, a partir do menu de contexto, selecione **Add->Controller**.

    ![Adicione um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. No diálogo **Add Scaffold,** selecione **Controlador MVC 5 - Vazio**, e selecione **Adicionar**.

    ![Especificar o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. No diálogo do **Controlador adicionar,** nomeie o *controlador de*controlo , e selecione **Adicionar**.

    ![Nomeie o controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adicione as seguintes diretivas *utilizando* o `QueuesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Criar uma fila

Os seguintes passos ilustram como criar uma fila:

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`. 

1. Adicione um método chamado **CreateQueue** que devolve um **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateQueue,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenha um objeto **CloudQueue** que represente uma referência ao nome de fila pretendido. O método **CloudQueueClient.GetQueueReference** não faz um pedido contra o armazenamento de fila. A referência é devolvida se a fila existe ou não. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ligue para o método **CloudQueue.CreateIfNotExists** para criar a fila se ainda não existir. O método **CloudQueue.CreateIfNotExists** retorna **verdadeiro** se a fila não existir e é criada com sucesso. Caso contrário, **o falso** é devolvido.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **createqueue** para o nome da visualização e selecione **Adicionar**.

1. Abra `CreateQueue.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **Criar fila** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Criar fila](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Como mencionado anteriormente, o método **CloudQueue.CreateIfNotExists** retorna **verdadeiro apenas** quando a fila não existe e é criada. Portanto, se executar a aplicação quando a fila existe, o método devolve **falso**. Para executar a aplicação várias vezes, deve apagar a fila antes de executar novamente a aplicação. A eliminação da fila pode ser feita através do método **CloudQueue.Delete.** Também pode eliminar a fila utilizando o [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

Depois de [criar uma fila,](#create-a-queue)pode adicionar mensagens a essa fila. Esta secção leva-o através da adição de uma mensagem a uma *fila de testes de*fila . 

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **AddMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **AddMessage,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que represente uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Crie o objeto **CloudQueueMessage** que represente a mensagem que pretende adicionar à fila. Um objeto **CloudQueueMessage** pode ser criado a partir de uma cadeia (em formato UTF-8) ou de um matriz byte.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Ligue para o método **CloudQueue.AddMessage** para adicionar a mensagem à fila.

    ```csharp
    queue.AddMessage(message);
    ```

1. Crie e coloque um par de propriedades **Do ViewBag** para exibição na vista.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **addMessage** para o nome da visualização e selecione **Adicionar**.

1. Abra `AddMessage.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **Adicionar mensagem** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Adicionar mensagem](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

As duas secções - [Leia uma mensagem de uma fila sem a remover](#read-a-message-from-a-queue-without-removing-it) e leia e remova uma mensagem de uma [fila](#read-and-remove-a-message-from-a-queue) - ilustracomo ler mensagens de uma fila.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Leia uma mensagem de uma fila sem removê-la

Esta secção ilustra como espreitar uma mensagem em fila (leia a primeira mensagem sem a remover).  

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **PeekMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **PeekMessage,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que represente uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ligue para o método **CloudQueue.PeekMessage** para ler a primeira mensagem na fila sem removê-la da fila. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Atualize o **ViewBag** com dois valores: o nome da fila e a mensagem que foi lida. O objeto **CloudQueueMessage** expõe duas propriedades para obter o valor do objeto: **CloudQueueMessage.AsBytes** e **CloudQueueMessage.AsString**. **AsString** (usado neste exemplo) devolve uma corda, enquanto **as Bytes** devolve matriz byte.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **peekMessage** para o nome da visualização e selecione **Adicionar**.

1. Abra `PeekMessage.cshtml`e modifique-o de modo a parecer o seguinte código:

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

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **mensagem Peek** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Mensagem de espreitar](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Leia e remova uma mensagem de uma fila

Nesta secção, aprende-se a ler e a remover uma mensagem de uma fila.   

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **ReadMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que represente uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ligue para o método **CloudQueue.GetMessage** para ler a primeira mensagem na fila. O método **CloudQueue.GetMessage** torna a mensagem invisível durante 30 segundos (por padrão) a quaisquer outras mensagens de leitura de código, de modo a que nenhum outro código possa modificar ou apagar a mensagem durante o seu processamento. Para alterar a quantidade de tempo em que a mensagem é invisível, modifique o parâmetro **timeout** de visibilidade que está a ser passado para o método **CloudQueue.GetMessage.**

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Ligue para o **cloudQueueMessage.Elimine** o método para apagar a mensagem da fila.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Atualize o **ViewBag** com a mensagem apagada e o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **o ReadMessage** para o nome da visualização e selecione **Adicionar**.

1. Abra `ReadMessage.cshtml`e modifique-o de modo a parecer o seguinte código:

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

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **mensagem Ler/Excluir** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Ler e apagar mensagem](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Esta secção ilustra como obter o comprimento da fila (número de mensagens). 

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **GetQueueLength** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que represente uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ligue para o método **CloudQueue.FetchAttributes** para recuperar os atributos da fila (incluindo o seu comprimento). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Aceda à propriedade **CloudQueue.ApproximateMessageCount** para obter o comprimento da fila.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Atualize o **ViewBag** com o nome da fila e o seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **GetQueueLength** para o nome da visualização e selecione **Adicionar**.

1. Abra `GetQueueLengthMessage.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **Obter o comprimento da fila** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Obtenha o comprimento da fila](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminar uma fila
Esta secção ilustra como apagar uma fila. 

> [!NOTE]
> 
> Esta secção pressupõe que tenha concluído os passos [Configurar o ambiente de desenvolvimento.](#set-up-the-development-environment) 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método chamado **DeleteQueue** que devolve um **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **DeleteQueue,** obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de * &lt;* ligação de armazenamento e conta de armazenamento da configuração do serviço Azure: (Alterar o nome da conta de armazenamento>para o nome da conta de armazenamento Azure a que acede.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** representa um cliente de serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que represente uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Ligue para o método **CloudQueue.Elimine** o método para eliminar a fila representada pelo objeto **CloudQueue.**

    ```csharp
    queue.Delete();
    ```

1. Atualize o **ViewBag** com o nome da fila e o seu comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. No **Solution Explorer,** expanda a pasta **Views,** as **filas**de cliques à direita, e a partir do menu de contexto, selecione **Add->View**.

1. No diálogo **Add View,** introduza **DeleteQueue** para o nome da visualização e selecione **Adicionar**.

1. Abra `DeleteQueue.cshtml`e modifique-o de modo a parecer o seguinte código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. No **Solution Explorer,** expanda a pasta Partilha `_Layout.cshtml`de **Visualizações->** e abra.

1. Depois do último **Html.ActionLink,** adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecionar **Obter o comprimento da fila** para ver resultados semelhantes aos seguintes screenshot:
  
    ![Apagar a fila](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Inicie-se com o armazenamento de blob Azure e serviços conectados do Estúdio Visual (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Inicie-se com armazenamento de mesa Azure e Serviços Conectados de Estúdio Visual (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
