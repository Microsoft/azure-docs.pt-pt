---
title: Como utilizar o armazenamento de filas do Java | Documentos da Microsoft
description: Saiba como utilizar o serviço de fila do Azure para criar e eliminar filas, inserir, obter e eliminar mensagens. Exemplos de escritos em Java.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7e422825c6aa66b14f07f1e52bdd828971ad5eb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012367"
---
# <a name="how-to-use-queue-storage-from-java"></a>Como utilizar o Armazenamento de filas do Java
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Descrição geral
Este guia irá mostrar como realizar cenários comuns utilizando o serviço de armazenamento de filas do Azure. Os exemplos são escritos em Java e utilizam [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java). Os cenários abrangidos incluem **inserindo**, **observação**, **introdução**, e **a eliminar** fila de mensagens, que  **criando** e **eliminar** filas. Para obter mais informações sobre as filas, consulte a [próximos passos](#Next-Steps) secção.

Nota: Está disponível um SDK para os programadores que utilizem o Armazenamento do Azure em dispositivos Android. Para obter mais informações, veja o [SDK do Armazenamento do Azure para Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Neste guia, irá utilizar recursos de armazenamento que podem ser executados dentro de um aplicativo Java localmente, ou no código em execução numa função da web ou função de trabalho no Azure.

Para tal, terá de instalar o Kit de desenvolvimento Java (JDK) e criar uma conta de armazenamento do Azure na sua subscrição do Azure. Assim que tiver feito isso, precisará verificar se o sistema de desenvolvimento cumpre os requisitos mínimos e dependências que estão listadas na [SDK de armazenamento do Azure para Java] [ Azure Storage SDK for Java] repositório no GitHub. Se o seu sistema cumpre os requisitos, pode seguir as instruções para transferir e instalar as bibliotecas de armazenamento do Azure para Java no seu sistema desse repositório. Depois de concluir essas tarefas, será capaz de criar uma aplicação de Java que utiliza os exemplos neste artigo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de fila
Adicione as seguintes declarações de importação na parte superior do ficheiro de Java em que pretende utilizar APIs de armazenamento do Azure para aceder a filas:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Quando em execução num aplicativo cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da conta de armazenamento e a chave de acesso primária para a conta de armazenamento estão listados na [Portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Num aplicativo em execução dentro de uma função no Microsoft Azure, essa cadeia de caracteres pode ser armazenada no ficheiro de configuração do serviço, *serviceconfiguration. Cscfg*e podem ser acedidos com uma chamada para o  **RoleEnvironment.getConfigurationSettings** método. Eis um exemplo da obtenção da cadeia de ligação a partir de um elemento **Setting** (Definição) denominado *StorageConnectionString* no ficheiro de configuração do serviço:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
R **CloudQueueClient** objeto permite-lhe obter objetos de referência para filas. O código seguinte cria um **CloudQueueClient** objeto. (Nota: Existem outras formas de criar **CloudStorageAccount** objetos; para obter mais informações, consulte **CloudStorageAccount** no [Azure Storage Client SDK Reference].)

Utilizar o **CloudQueueClient** objeto para obter uma referência para a fila que pretende utilizar. Se não existir, é possível criar a fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Como: Adicione uma mensagem numa fila
Para introduzir uma mensagem numa fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, chame o **addMessage** método. R **CloudQueueMessage** podem ser criados a partir de uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes. Eis o código que cria uma fila (se não existir) e insere a mensagem "Olá, mundo".

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualização da mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila chamando **peekMessage**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

As pesquisas de exemplo de código seguintes através da fila de mensagens, localiza a primeira mensagem que corresponde a "Olá, mundo" para o conteúdo, em seguida, modifica a conteúdo de mensagem e é encerrado.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Em alternativa, o código de exemplo seguinte atualiza apenas a primeira mensagem visível na fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **downloadAttributes** método pede ao serviço de fila para vários valores atuais, incluindo uma contagem de quantas mensagens estão numa fila. A contagem é apenas aproximada, porque as mensagens podem ser adicionadas ou removidas depois do serviço de fila responde ao seu pedido. O **getApproximateMessageCount** método retorna o último valor obtido pela chamada para **downloadAttributes**, sem chamar o serviço de fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover da fila a mensagem seguinte
O código de retira da fila em dois passos. Quando chama **retrieveMessage**, obterá a seguinte mensagem numa fila. Uma mensagem devolvida por **retrieveMessage** torna-se invisível para qualquer outro código lendo as mensagens desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para concluir a remover a mensagem da fila, também tem de chamar **deleteMessage**. Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. A código chama **deleteMessage** imediatamente após a mensagem foi processada.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remoção da fila de mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem.

O seguinte código de exemplo utiliza a **retrieveMessages** método para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem, usando uma **para** loop. Também define o tempo limite de invisibilidade para cinco minutos (300 segundos) para cada mensagem. Tenha em atenção que é iniciado os cinco minutos para todas as mensagens ao mesmo tempo, então, quando cinco minutos tenham passado desde a chamada para **retrieveMessages**, as mensagens que não foram eliminadas ficarão visíveis novamente.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Como: Listar as filas
Para obter uma lista das filas atuais, chame o **CloudQueueClient.listQueues()** método, que irá devolver uma coleção de **CloudQueue** objetos.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o **deleteIfExists** método no **CloudQueue** objeto.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

* [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java)
* [Azure Storage Client SDK Reference][Azure Storage Client SDK Reference] (Referência do SDK do Cliente do Armazenamento do Azure)
* [REST API dos serviços do armazenamento do Azure][Azure Storage Services REST API]
* [Azure Storage Team Blog][Azure Storage Team Blog] (Blogue da Equipa do Armazenamento do Azure)

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/ (Referência do SDK do Cliente do Armazenamento do Azure)
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
