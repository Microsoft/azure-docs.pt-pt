---
title: Como utilizar o armazenamento da fila da Java - Azure Storage
description: Aprenda a usar o armazenamento da fila para criar e apagar filas. Aprenda a inserir, espreitar, obter e apagar mensagens com a biblioteca de clientes do Azure Storage para Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 78e2bd80b689394b654a85ea913c618c08120091
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854013"
---
# <a name="how-to-use-queue-storage-from-java"></a>Como utilizar o Armazenamento de filas do Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Este guia irá mostrar-lhe como codificar cenários comuns utilizando o serviço de armazenamento Azure Queue. Os exemplos são escritos em Java e utilizam [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java). Os cenários incluem **inserir,** **espreitar,** **receber**e apagar mensagens **de** fila. O código para **criar** e **eliminar** filas também está coberto. Para obter mais informações sobre as filas, consulte a secção [etapas seguintes.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java

# <a name="java-v12"></a>[Java v12](#tab/java)

Em primeiro lugar, verifique se o seu sistema de desenvolvimento cumpre os pré-requisitos listados na [biblioteca de clientes de armazenamento Azure Queue para Java v12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Para criar uma aplicação Java chamada *queues-how-to-v12*:

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize a Maven para criar uma nova aplicação de consola com o nome *filas-como-v12*. Digite o seguinte comando **mvn** para criar um "Olá mundo!" Projeto Java.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. A produção da geração do projeto deve ser algo assim:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro *pom.xml* no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java v8](#tab/java8)

Em primeiro lugar, verifique se o seu sistema de desenvolvimento cumpre os requisitos pré-requisitos listados no [Azure Storage SDK para Java v8](https://github.com/azure/azure-storage-java). Siga as instruções para descarregar e instalar as Bibliotecas de Armazenamento Azure para Java. Em seguida, pode criar uma aplicação Java usando os exemplos deste artigo.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao armazenamento da fila

Adicione as seguintes declarações de importação ao topo do ficheiro Java onde pretende utilizar APIs de armazenamento Azure para aceder às filas:

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure

Um cliente de armazenamento Azure usa uma cadeia de conexão de armazenamento para aceder a serviços de gestão de dados. Obtenha o nome e a chave de acesso primário para a sua conta de armazenamento listada no [portal Azure](https://portal.azure.com). Utilize-os como o *Nome de Conta* e os valores *'ContaKey'* no fio de ligação. Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```
Pode armazenar esta cadeia no ficheiro de config de serviço chamado *ServiceConfiguration.cscfg*. Para uma aplicação executada dentro de uma função Microsoft Azure, aceda à cadeia de conexão chamando **RoleEnvironment.getConfigurationSettings**. Aqui está um exemplo de obter a cadeia de conexão de um elemento de **definição** chamado *StorageConnectionString*:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

As seguintes amostras assumem que tem um objeto **de corda** contendo a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

# <a name="java-v12"></a>[Java v12](#tab/java)

Um objeto **queueClient** contém as operações para interagir com uma fila. O seguinte código cria um objeto **QueueClient.** Utilize o objeto **QueueClient** para criar a fila que pretende utilizar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

Um objeto **CloudQueueClient** permite obter objetos de referência para filas. O seguinte código cria um objeto **CloudQueueClient.** (Nota: Existem formas adicionais de criar objetos **CloudStorageAccount;** para mais informações, consulte **cloudStorageAccount** na [referência SDK do cliente de armazenamento Azure].)

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

---

## <a name="how-to-add-a-message-to-a-queue"></a>Como: Adicionar uma mensagem a uma fila

# <a name="java-v12"></a>[Java v12](#tab/java)

Para inserir uma mensagem numa fila existente, ligue para o método **de mensagens.** Uma mensagem pode ser uma cadeia (em formato UTF-8) ou uma matriz de byte. Aqui está o código que envia uma mensagem de corda para a fila.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

Para introduzir uma mensagem numa fila existente, primeiro crie um novo **CloudQueueMessage**. Em seguida, chame o método **de adição de sessage.** É possível criar uma **CloudQueueMessage** a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes. Aqui está o código que cria uma fila (se não existir) e insere a mensagem "Olá, Mundo".

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

---

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem

Pode espreitar a mensagem na frente de uma fila sem a retirar da fila chamando **peekMessage**.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

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

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representar uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado. O código seguinte atualiza uma mensagem de fila com novos conteúdos e define o tempo limite de visibilidade para prolongar mais 30 segundos. O alargamento do tempo limite de visibilidade dá ao cliente mais 30 segundos para continuar a trabalhar na mensagem. Também podes manter uma contagem de repetição. Se a mensagem for novamente julgada mais do que *n* vezes, apagará-a. Este cenário protege contra uma mensagem que desencadeia um erro de aplicação cada vez que é processado.

# <a name="java-v12"></a>[Java v12](#tab/java)

A amostra de código que se segue procura através da fila de mensagens, localiza o primeiro conteúdo de mensagem que corresponde a uma cadeia de pesquisa, modifica o conteúdo da mensagem e sai.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

A amostra de código que se segue procura através da fila de mensagens, localiza o primeiro conteúdo de mensagem que corresponde a "Olá, Mundo", modifica o conteúdo da mensagem e sai.

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

---

A amostra de código a seguir atualiza apenas a primeira mensagem visível na fila.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

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

---

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila.

# <a name="java-v12"></a>[Java v12](#tab/java)

O método **getProperties** pede ao serviço De Fila vários valores atuais. Um dos valores é a contagem de quantas mensagens estão numa fila. A contagem só é aproximada porque as mensagens podem ser adicionadas ou removidas após o seu pedido. O método **getApproximateMessageCount** devolve o último valor recuperado pela chamada para **obterProperties,** sem chamar o serviço de Fila.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

O **método downloadAttributes** pede ao serviço Queue vários valores atuais. Um dos valores é a contagem de quantas mensagens estão numa fila. A contagem só é aproximada porque as mensagens podem ser adicionadas ou removidas após o seu pedido. O método **getApproximateMessageCount** devolve o último valor recuperado pela chamada para **descarregarTributes,** sem chamar o serviço De Fila.

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

---

## <a name="how-to-dequeue-the-next-message"></a>Como: Dequeue a próxima mensagem

# <a name="java-v12"></a>[Java v12](#tab/java)

O seu código desmente uma mensagem de uma fila em dois passos. Quando ligar **para receber AMessage,** recebe a próxima mensagem numa fila. Uma mensagem devolvida a partir de **receber A Mensagens** torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar a remoção da mensagem da fila, também deve chamar **deleteMessage**. Se o seu código não processar uma mensagem, este processo em duas etapas garante que pode receber a mesma mensagem e tentar novamente. O seu código chama **apagar A mensagens** logo após o processo da mensagem.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

O seu código desmente uma mensagem de uma fila em dois passos. Quando ligar para **o "Recuperar"** e receber a próxima mensagem numa fila. Uma mensagem devolvida de **recuperar A Caixa** torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar a remoção da mensagem da fila, também deve chamar **deleteMessage**. Se o seu código não processar uma mensagem, este processo em duas etapas garante que pode receber a mesma mensagem e tentar novamente. O seu código chama **apagar A mensagens** logo após o processo da mensagem.

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

---

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desescodução de mensagens

Há duas formas de personalizar a recuperação de mensagens a partir de uma fila. Primeiro, obtenha um lote de mensagens (até 32). Em segundo lugar, desacione um tempo de invisibilidade mais longo ou mais curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem.

# <a name="java-v12"></a>[Java v12](#tab/java)

O seguinte exemplo de código utiliza o método **de receber mensagens** para receber 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um **loop.** Também define o tempo limite de invisibilidade para cinco minutos (300 segundos) para cada mensagem. O tempo limite começa para todas as mensagens ao mesmo tempo. Quando passarem cinco minutos desde a chamada para **receber mensagens,** quaisquer mensagens não apagadas voltarão a ser visíveis.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

O seguinte exemplo de código utiliza o método **recuperar caixas** para obter 20 mensagens numa única chamada. Em seguida, processa cada mensagem usando um **loop.** Também define o tempo limite de invisibilidade para cinco minutos (300 segundos) para cada mensagem. O tempo limite começa para todas as mensagens ao mesmo tempo. Quando passarem cinco minutos desde a chamada para **recuperar as mensagens,** quaisquer mensagens não apagadas voltarão a ser visíveis.

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

---

## <a name="how-to-list-the-queues"></a>Como: Listar as filas

# <a name="java-v12"></a>[Java v12](#tab/java)

Para obter uma lista das filas atuais, ligue para o método **QueueServiceClient.listQueues,que** devolverá uma coleção de objetos **QueueItem.**

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

Para obter uma lista das filas atuais, ligue para o método **CloudQueueClient.listQueues,que** devolverá uma coleção de objetos **CloudQueue.**

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

---

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila

# <a name="java-v12"></a>[Java v12](#tab/java)

Para eliminar uma fila e todas as mensagens contidas na sua parte, ligue para o método **de eliminação** no objeto **QueueClient.**

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

Para eliminar uma fila e todas as mensagens contidas na sua casa, ligue para o método **deleteIfExists** no objeto **CloudQueue.**

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

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

* [Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK do Armazenamento do Azure para Java)
* [Azure Storage Client SDK Reference][Azure Storage Client SDK Reference] (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure][Azure Storage Services REST API]
* [Blog da equipa de armazenamento Azure][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Azure Storage Client SDK Reference]: https://azure.github.io/azure-sdk-for-java/storage.html (Referência do SDK do Cliente do Armazenamento do Azure)
[Azure Storage Services REST API]: https://docs.microsoft.com/rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
