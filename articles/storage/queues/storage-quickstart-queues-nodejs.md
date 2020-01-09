---
title: 'Início rápido: biblioteca de armazenamento de fila do Azure V12-JavaScript'
description: Saiba como usar a biblioteca JavaScript V12 do Azure Queue para criar uma fila e adicionar mensagens à fila. Em seguida, você aprende a ler e excluir mensagens da fila. Você também aprenderá como excluir uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 9f666376020d36bd57b2cc9e78a0704a8ec47b4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473123"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Início rápido: biblioteca de cliente de armazenamento de fila do Azure V12 para JavaScript

Introdução à biblioteca de cliente de armazenamento de filas do Azure versão 12 para JavaScript. O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente de armazenamento de fila do Azure V12 para JavaScript para:

* Criar uma fila
* Adicionar mensagens a uma fila
* Inspecionar mensagens em uma fila
* Atualizar uma mensagem em uma fila
* Receber mensagens de uma fila
* Excluir mensagens de uma fila
* Eliminar uma fila

[Documentação de referência de API](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-queue) | [exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Node. js](https://nodejs.org/en/download/) atual para seu sistema operacional.

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente de armazenamento de filas do Azure V12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo node. js chamado *filas-início rápido-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Alterne para o diretório filas recém-criadas *-início rápido-V12* .

    ```console
    cd queues-quickstart-v12
    ```

1. Crie um novo arquivo de texto chamado *Package. JSON*. Esse arquivo define o projeto Node. js. Salve esse arquivo no diretório *Queues-QuickStart-V12* . Aqui está o conteúdo do arquivo:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Você pode colocar seu próprio nome no campo `author`, se desejar.

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório *Queues-QuickStart-V12* , instale a biblioteca de cliente de armazenamento de filas do Azure para o pacote JavaScript usando o comando `npm install`.

```console
npm install
```

 Esse comando lê o arquivo *Package. JSON* e instala a biblioteca de cliente do armazenamento de filas do Azure V12 para o pacote JavaScript e todas as bibliotecas das quais ela depende.

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abrir outro arquivo de texto novo no editor de código
1. Adicionar chamadas de `require` para carregar os módulos do Azure e do node. js
1. Crie a estrutura para o programa, incluindo manipulação de exceção muito básica

    Este é o código:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Salve o novo arquivo como *Queues-QuickStart-V12. js* no diretório *Queues-QuickStart-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona. O armazenamento de filas oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de filas](./media/storage-queues-introduction/queue1.png)

Use as seguintes classes JavaScript para interagir com estes recursos:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): o `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): a classe `QueueClient` permite que você gerencie e manipule uma fila individual e suas mensagens.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): a classe `QueueMessage` representa os objetos individuais retornados ao chamar [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como fazer as seguintes ações com a biblioteca de cliente de armazenamento de filas do Azure para JavaScript:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
* [Inspecionar mensagens em uma fila](#peek-at-messages-in-a-queue)
* [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber mensagens de uma fila](#receive-messages-from-a-queue)
* [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
* [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento da variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro da função `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor UUID ao nome da fila para garantir que seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre como nomear filas, consulte [nomeando filas e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) . Em seguida, chame o método [Create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) para criar a fila em sua conta de armazenamento.

Adicione este código ao final da função `main`:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O trecho de código a seguir adiciona mensagens à fila chamando o método [SendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) . Ele também salva o [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) retornado da terceira chamada de `sendMessage`. O `sendMessageResponse` retornado é usado para atualizar o conteúdo da mensagem posteriormente no programa.

Adicione este código ao final da função `main`:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Inspecionar mensagens em uma fila

Inspecione as mensagens na fila chamando o método [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) . O método `peekMessages` recupera uma ou mais mensagens da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final da função `main`:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) . O método `updateMessage` pode alterar o tempo limite e o conteúdo da visibilidade de uma mensagem. O conteúdo da mensagem deve ser uma cadeia de caracteres codificada em UTF-8 que tenha até 64 KB de tamanho. Junto com o novo conteúdo, passe `messageId` e `popReceipt` da resposta que foi salva anteriormente no código. As propriedades `sendMessageResponse` identificam a mensagem a ser atualizada.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe mensagens adicionadas anteriormente chamando o método [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) .  No campo `numberOfMessages`, passe o número máximo de mensagens a serem recebidas para esta chamada.

Adicione este código ao final da função `main`:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua as mensagens da fila depois que elas forem recebidas e processadas. Nesse caso, o processamento está apenas exibindo a mensagem no console.

Exclua as mensagens chamando o método [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) . Todas as mensagens que não forem explicitamente excluídas se tornarão visíveis na fila novamente para outra oportunidade de processá-las.

Adicione este código ao final da função `main`:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Eliminar uma fila

O código a seguir limpa os recursos que o aplicativo criou excluindo a fila usando o método [delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) .

Adicione esse código ao final da função `main` e salve o arquivo:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila, depois as recupera e exclui, antes de excluir a fila.

Na janela do console, navegue até o diretório que contém o arquivo *Queues-QuickStart-V12. js* e execute o comando `node` a seguir para executar o aplicativo.

```console
node queues-quickstart-v12.js
```

A saída do aplicativo é semelhante ao exemplo a seguir:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Percorra o código em seu depurador e verifique sua [portal do Azure](https://portal.azure.com) durante todo o processo. Verifique sua conta de armazenamento para verificar se as mensagens na fila são criadas e excluídas.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando código JavaScript. Em seguida, você aprendeu a inspecionar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite:

> [!div class="nextstepaction"]
> [Documentação do Azure para JavaScript](https://docs.microsoft.com/azure/javascript/)

* Para saber mais, consulte a [biblioteca de cliente da fila de armazenamento do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Para ver mais aplicativos de exemplo de armazenamento de filas do Azure, vá para [biblioteca de cliente de armazenamento de filas do Azure exemplos de JavaScript V12](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
