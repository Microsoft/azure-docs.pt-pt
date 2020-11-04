---
title: 'Quickstart: Azure Queue storage library v12 - Java'
description: Saiba como usar a biblioteca Azure Queue Java v12 para criar uma fila e adicionar mensagens à fila. Em seguida, aprende-se a ler e a apagar mensagens da fila. Também aprenderá a apagar uma fila.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 25b6bdcb293379e3206cc6714fae65fe40f6e6c5
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345606"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Quickstart: Azure Queue storage client library v12 for Java

Começa com a versão 12 da biblioteca do cliente de armazenamento da Fila Azure para a Java. O armazenamento da fila Azure é um serviço para armazenar um grande número de mensagens para posterior recuperação e processamento. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes de armazenamento Azure Queue v12 para:

- Criar uma fila
- Adicione mensagens a uma fila
- Espreite as mensagens em uma fila
- Atualize uma mensagem em uma fila
- Receber e apagar mensagens de uma fila
- Eliminar uma fila

Recursos adicionais:

- [Documentação de referência da API](/java/api/overview/azure/storage-queue-readme)
- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Amostras](../common/storage-samples-java.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento Azure - [crie uma conta de armazenamento](../common/storage-account-create.md)

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Queue v12 para Java.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Java chamada *queues-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize a Maven para criar uma nova aplicação de consola com o nome *de filas-quickstart-v12*. Digite o seguinte comando **mvn** para criar um "Olá mundo!" Projeto Java.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Mude para o diretório *de filas-quickstart-v12 recém-criado.*

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro *pom.xml* no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Navegue para o */src/main/java/com/queues/quickstart* diretório
1. Abra o ficheiro *App.java* no seu editor
1. Apagar a `System.out.println("Hello world!");` declaração
1. Adicionar `import` diretivas

Aqui está o código:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos. O armazenamento de fila oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama de arquitetura de armazenamento de fila](./media/storage-queues-introduction/queue1.png)

Utilize as seguintes classes java para interagir com estes recursos:

- [QueueClientBuilder](/java/api/com.azure.storage.queue.queueclientbuilder): A `QueueClientBuilder` classe configura e instantanea um `QueueClient` objeto.
- [QueueServiceClient](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` Permite-lhe gerir todas as filas na sua conta de armazenamento.
- [QueueClient](/java/api/com.azure.storage.queue.queueclient): A `QueueClient` classe permite-lhe gerir e manipular uma fila individual e as suas mensagens.
- [QueueMessageItem](/java/api/com.azure.storage.queue.models.queuemessageitem): A `QueueMessageItem` classe representa os objetos individuais devolvidos ao chamar [as caixas de correio](/java/api/com.azure.storage.queue.queueclient.receivemessages) numa fila.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como fazer as seguintes ações com a biblioteca de clientes de armazenamento Azure Queue para Java:

- [Obter a cadeia de ligação](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicione mensagens a uma fila](#add-messages-to-a-queue)
- [Espreite as mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualize uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber e apagar mensagens de uma fila](#receive-and-delete-messages-from-a-queue)
- [Eliminar uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento. A cadeia de ligação é armazenada a variável ambiente criada na secção [de cadeia de ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `main` método:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Criar uma fila

Decida um nome para a nova fila. O código abaixo anexa um valor GUID ao nome da fila para garantir que é único.

> [!IMPORTANT]
> Os nomes da fila só podem conter letras minúsculas, números e hífens, e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre o nome de filas, consulte [As Filas de Nomeação e Os Metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie um exemplo da classe [QueueClient.](/java/api/com.azure.storage.queue.queueclient) Em seguida, ligue para o método [de criação](/java/api/com.azure.storage.queue.queueclient.create) para criar a fila na sua conta de armazenamento.

Adicione este código ao fim do `main` método:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Adicione mensagens a uma fila

O seguinte corte de código adiciona mensagens à fila, chamando o método [de envio de mensagens.](/java/api/com.azure.storage.queue.queueclient.sendmessage) Também guarda um [SendMessageResult](/java/api/com.azure.storage.queue.models.sendmessageresult) devolvido de uma `sendMessage` chamada. O resultado é utilizado para atualizar a mensagem mais tarde no programa.

Adicione este código ao fim do `main` método:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Espreite as mensagens em uma fila

Espreite as mensagens na fila chamando o método [peekMessages.](/java/api/com.azure.storage.queue.queueclient.peekmessages) O `peelkMessages` método recupera uma ou mais mensagens da parte da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao fim do `main` método:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Atualize uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [de mensagens de atualização.](/java/api/com.azure.storage.queue.queueclient.updatemessage) O `updateMessage` método pode alterar o tempo limite de visibilidade e o conteúdo de uma mensagem. O conteúdo da mensagem deve ser uma cadeia codificada UTF-8 que tem até 64 KB de tamanho. Juntamente com novos conteúdos para a mensagem, passe no ID da mensagem e no recibo pop utilizando o `SendMessageResult` que foi guardado anteriormente no código. O ID da mensagem e o recibo pop identificam a mensagem a atualizar.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Receber e apagar mensagens de uma fila

Descarregue mensagens previamente adicionadas chamando o método [de receber mensagens.](/java/api/com.azure.storage.queue.queueclient.receivemessages) O código de exemplo também elimina as mensagens da fila depois de serem recebidas e processadas. Neste caso, o processamento é apenas exibindo a mensagem na consola.

A aplicação faz uma pausa para a entrada do utilizador, ligando `System.console().readLine();` antes de receber e elimina as mensagens. Verifique no seu [portal Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem eliminados. Quaisquer mensagens não explicitamente apagadas acabarão por se tornar visíveis na fila novamente para outra oportunidade de as processar.

Adicione este código ao fim do `main` método:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Eliminar uma fila

O código seguinte limpa os recursos que a app criou eliminando a fila utilizando o método [de eliminação.](/java/api/com.azure.storage.queue.queueclient.delete)

Adicione este código ao fim do `main` método:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria e adiciona três mensagens a uma fila Azure. O código lista as mensagens na fila e, em seguida, recupera-as e apaga-as, antes de finalmente apagar a fila.

Na janela da consola, navegue para o seu diretório de aplicações e, em seguida, construa e execute a aplicação.

```console
mvn compile
```

Então, construa o pacote.

```console
mvn package
```

Execute o seguinte `mvn` comando para executar a aplicação.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Quando a aplicação parar antes de receber mensagens, consulte a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Prima a tecla **'Inserir'** para receber e eliminar as mensagens. Quando solicitado, prima novamente a tecla **Enter** para apagar a fila e terminar a demonstração.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar uma fila e a adicionar-lhe mensagens usando o código Java. Depois aprendeu a espreitar, a recuperar e a apagar mensagens. Finalmente, aprendeu a apagar uma fila de mensagens.

Para tutoriais, amostras, arranques rápidos e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure for Java cloud developers](/azure/developer/java/) (Azure para programadores de Java na cloud)

- Para ver mais aplicativos de amostra de armazenamento da Azure Queue, continue até a [Azure Queue armazenamento amostras de biblioteca de clientes SDK v12 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
