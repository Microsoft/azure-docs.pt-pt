---
title: Como utilizar o armazenamento de fila a partir de Java - Armazenamento Azure
description: Aprenda a usar o armazenamento de fila para criar e apagar filas, inserir, obter e apagar mensagens com a biblioteca de clientes do Azure Storage para Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 7658b8541e7a79a5e547a6649b35681446e34b0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067137"
---
# <a name="how-to-use-queue-storage-from-java"></a>Como utilizar o Armazenamento de filas do Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

Este guia irá mostrar-lhe como realizar cenários comuns utilizando o serviço de armazenamento de fila Azure. Os exemplos são escritos em Java e utilizam [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java). Os cenários abordados incluem **a inserção,** **espreitar,** **receber**e **apagar** mensagens de fila, bem como **criar** e **apagar** filas. Para mais informações sobre as filas, consulte a secção [Degraus Seguintes.](#next-steps)

> [!NOTE]
> Está disponível um SDK para os programadores que utilizem o Armazenamento do Azure em dispositivos Android. Para obter mais informações, veja o [SDK do Armazenamento do Azure para Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java

Neste guia, utilizará funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação Java localmente, ou em código que funciona dentro de uma aplicação web em Azure.

Para isso, terá de instalar o Java Development Kit (JDK) e criar uma conta de armazenamento Azure na sua subscrição Azure. Uma vez feito, terá de verificar se o seu sistema de desenvolvimento satisfaz os requisitos mínimos e dependências que estão listados no [SDK][Azure Storage SDK for Java] de Armazenamento Azure para o repositório Java no GitHub. Se o seu sistema cumprir esses requisitos, pode seguir as instruções para descarregar e instalar as Bibliotecas de Armazenamento Azure para Java no seu sistema a partir desse repositório. Uma vez concluídas essas tarefas, poderá criar uma aplicação Java que utilize os exemplos neste artigo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao armazenamento de filas

Adicione as seguintes declarações de importação ao topo do ficheiro Java onde pretende utilizar APIs de armazenamento Azure para aceder às filas:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure

Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Se estiver a executar numa aplicação cliente, tem de fornecer a cadeia de ligação de armazenamento no formato seguinte, com o nome da conta de armazenamento e a Chave de acesso primária dessa conta indicada no [portal do Azure](https://portal.azure.com) nos valores *AccountName* e *AccountKey*. Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Numa aplicação que funciona dentro de uma função no Microsoft Azure, esta cadeia pode ser armazenada no ficheiro de configuração do serviço, *ServiceConfiguration.cscfg,* e pode ser acedida com uma chamada para o método **RoleEnvironment.getConfigurationSettings.** Eis um exemplo da obtenção da cadeia de ligação a partir de um elemento **Setting** (Definição) denominado *StorageConnectionString* no ficheiro de configuração do serviço:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
Um objeto **CloudQueueClient** permite obter objetos de referência para filas. O seguinte código cria um objeto **CloudQueueClient.** (Nota: Existem formas adicionais de criar objetos **CloudStorageAccount;** para mais informações, consulte **cloudStorageAccount** no [Azure Storage Client SDK Reference].)

Utilize o objeto **CloudQueueClient** para obter uma referência à fila que pretende utilizar. Pode criar a fila se não existir.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Como: Adicionar uma mensagem a uma fila
Para introduzir uma mensagem numa fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, ligue para o método **addMessage.** É possível criar uma **CloudQueueMessage** a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes. Aqui está o código que cria uma fila (se não existe) e insere a mensagem "Olá, Mundo".

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

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem
Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. O prolongamento do tempo de visibilidade poupa o estado de trabalho associado à mensagem, e dá ao cliente mais um minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

A amostra de código seguinte procura através da fila de mensagens, localiza a primeira mensagem que corresponde a "Olá, Mundo" para o conteúdo, e depois modifica o conteúdo e saídas da mensagem.

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

Em alternativa, a amostra de código seguinte atualiza apenas a primeira mensagem visível na fila.

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
Pode obter uma estimativa do número de mensagens numa fila. O método **downloadAtributos** pede ao serviço de fila vários valores atuais, incluindo uma contagem de quantas mensagens estão numa fila. A contagem é aproximada porque as mensagens podem ser adicionadas ou removidas após o serviço de fila responder ao seu pedido. O método **getApproximateMessageCount** devolve o último valor recuperado pela chamada para **downloadAtributos,** sem ligar para o serviço fila.

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

## <a name="how-to-dequeue-the-next-message"></a>Como: Desfilar a próxima mensagem
O seu código dique uma mensagem de uma fila em dois passos. Quando ligar para **o retrieveMessage,** recebe a próxima mensagem numa fila. Uma mensagem devolvida do **retrieveMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve ligar para **apagarMensagem**. Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. As chamadas de código **eliminam o Message** logo após a mensagem ter sido processada.

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

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desfilar mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem.

O exemplo de código que se segue utiliza o método **de recuperação DeMensagens** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um **para** loop. Também define o tempo de invisibilidade para cinco minutos (300 segundos) para cada mensagem. Os cinco minutos começam para todas as mensagens ao mesmo tempo, pelo que, quando passaram cinco minutos desde a chamada para **recuperar Mensagens,** quaisquer mensagens que não tenham sido apagadas voltarão a ser visíveis.

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
Para obter uma lista das filas atuais, ligue para o método **CloudQueueClient.listQueues()** que devolverá uma coleção de objetos **CloudQueue.**

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

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila
Para eliminar uma fila e todas as mensagens contidas na seleção, ligue para o método **deleteIfExists** no objeto **CloudQueue.**

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

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

* [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java)
* [Azure Storage Client SDK Reference][Azure Storage Client SDK Reference] (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure][Azure Storage Services REST API]
* [Blog da equipe de armazenamento azure][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html (Referência do SDK do Cliente do Armazenamento do Azure)
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
