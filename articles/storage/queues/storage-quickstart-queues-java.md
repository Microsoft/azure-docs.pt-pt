---
title: 'Início rápido: biblioteca de armazenamento de fila do Azure V12-Java'
description: Saiba como usar a biblioteca Java V12 da fila do Azure para criar uma fila e adicionar mensagens à fila. Em seguida, você aprende a ler e excluir mensagens da fila. Você também aprenderá como excluir uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 2bbd66257e1ccd81f1dac7d775b4dd6ccc39189a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473137"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Início rápido: biblioteca de cliente de armazenamento de fila do Azure V12 para Java

Introdução à biblioteca de cliente de armazenamento de filas do Azure versão 12 para Java. O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente de armazenamento de fila do Azure V12 para Java para:

* Criar uma fila
* Adicionar mensagens a uma fila
* Inspecionar mensagens em uma fila
* Atualizar uma mensagem em uma fila
* Receber e excluir mensagens de uma fila
* Eliminar uma fila

[Documentação de referência de API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples) de | do Maven ( [código-fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [do pacote)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) da biblioteca

## <a name="prerequisites"></a>Pré-requisitos

* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior
* [Apache Maven](https://maven.apache.org/download.cgi)
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente de armazenamento de filas do Azure V12 para Java.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Java chamado *filas-início rápido-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), use o Maven para criar um novo aplicativo de console com o nome *filas-QuickStart-V12*. Digite o seguinte comando **MVN** para criar um "Olá, mundo!" Projeto Java.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. A saída da geração do projeto deve ser semelhante a esta:

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

1. Alterne para o diretório filas recém-criadas *-início rápido-V12* .

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo *pom. xml* em seu editor de texto. Adicione o elemento de dependência a seguir ao grupo de dependências.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Navegue até o diretório */src/main/java/com/Queues/QuickStart*
1. Abra o arquivo *app. java* em seu editor
1. Excluir a instrução `System.out.println("Hello world!");`
1. Adicionar `import` diretivas

Este é o código:

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

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona. O armazenamento de filas oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de filas](./media/storage-queues-introduction/queue1.png)

Use as classes Java a seguir para interagir com estes recursos:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): a classe `QueueClientBuilder` configura e instancia um objeto `QueueClient`.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): o `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): a classe `QueueClient` permite que você gerencie e manipule uma fila individual e suas mensagens.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): a classe `QueueMessageItem` representa os objetos individuais retornados ao chamar [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como fazer as seguintes ações com a biblioteca de cliente de armazenamento de filas do Azure para Java:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
* [Inspecionar mensagens em uma fila](#peek-at-messages -in-a-queue)
* [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber e excluir mensagens de uma fila](#receive-and-delete-messages-from-a-queue)
* [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento. A cadeia de conexão é armazenada na variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do método `main`:

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

Escolha um nome para a nova fila. O código a seguir acrescenta um valor de GUID ao nome da fila para garantir que seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre como nomear filas, consulte [nomeando filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).


Crie uma instância da classe [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) . Em seguida, chame o método [Create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) para criar a fila em sua conta de armazenamento.

Adicione este código ao final do método de `main`:

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

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O trecho de código a seguir adiciona mensagens à fila chamando o método [SendMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) . Ele também salva um [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) retornado de uma chamada `sendMessage`. O resultado é usado para atualizar a mensagem posteriormente no programa.

Adicione este código ao final do método de `main`:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Inspecionar mensagens em uma fila

Inspecione as mensagens na fila chamando o método [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) . O método `peelkMessages` recupera uma ou mais mensagens da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final do método de `main`:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [updateMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) . O método `updateMessage` pode alterar o tempo limite e o conteúdo da visibilidade de uma mensagem. O conteúdo da mensagem deve ser uma cadeia de caracteres codificada em UTF-8 que tenha até 64 KB de tamanho. Junto com o novo conteúdo da mensagem, passe a ID da mensagem e o recebimento do pop usando o `SendMessageResult` que foi salvo anteriormente no código. A ID da mensagem e o recebimento pop identificam a mensagem a ser atualizada.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Receber e excluir mensagens de uma fila

Baixe mensagens adicionadas anteriormente chamando o método [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) . O código de exemplo também exclui mensagens da fila depois que elas são recebidas e processadas. Nesse caso, o processamento está apenas exibindo a mensagem no console.

O aplicativo pausa a entrada do usuário chamando `System.console().readLine();` antes de receber e excluir as mensagens. Verifique no [portal do Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem excluídos. Todas as mensagens que não forem explicitamente excluídas se tornarão visíveis na fila novamente para outra oportunidade de processá-las.

Adicione este código ao final do método de `main`:

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

O código a seguir limpa os recursos que o aplicativo criou excluindo a fila usando o método [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) .

Adicione este código ao final do método de `main`:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila, depois as recupera e exclui, antes de excluir a fila.

Na janela do console, navegue até o diretório do aplicativo e, em seguida, compile e execute o aplicativo.

```console
mvn compile
```

Em seguida, compile o pacote.

```console
mvn package
```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A saída do aplicativo é semelhante ao exemplo a seguir:

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

Quando o aplicativo for pausado antes de receber mensagens, verifique sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Pressione a tecla **Enter** para receber e excluir as mensagens. Quando solicitado, pressione a tecla **Enter** novamente para excluir a fila e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando código Java. Em seguida, você aprendeu a inspecionar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite:

> [!div class="nextstepaction"]
> [Azure for Java cloud developers](https://docs.microsoft.com/azure/java/) (Azure para programadores de Java na cloud)

* Para ver mais aplicativos de exemplo de armazenamento de filas do Azure, vá para [Azure Queue Storage SDK V12 Java cliente de exemplos de biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
