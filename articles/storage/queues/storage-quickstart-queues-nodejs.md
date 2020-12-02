---
title: 'Quickstart: Azure Queue storage library v12 - JavaScript'
description: Saiba como utilizar a biblioteca Azure Queue JavaScript v12 para criar uma fila e adicionar mensagens à fila. Em seguida, aprende-se a ler e a apagar mensagens da fila. Também aprenderá a apagar uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 5f50e42bc33adb8f40520f3f98bf1dcfba190a41
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491915"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Quickstart: Azure Queue storage client library v12 for JavaScript

Começa com a versão 12 da biblioteca do cliente de armazenamento da Fila Azure para o JavaScript. O armazenamento da fila Azure é um serviço para armazenar um grande número de mensagens para posterior recuperação e processamento. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes de armazenamento Azure Queue v12 para JavaScript para:

- Criar uma fila
- Adicione mensagens a uma fila
- Espreite as mensagens em uma fila
- Atualize uma mensagem em uma fila
- Receber mensagens de uma fila
- Apagar mensagens de uma fila
- Eliminar uma fila

Recursos adicionais:

- [Documentação de referência da API](/javascript/api/@azure/storage-queue/)
- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-queue)
- [Amostras](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento Azure - [crie uma conta de armazenamento](../common/storage-account-create.md)
- A [Node.js](https://nodejs.org/en/download/) atual para o seu sistema operativo.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Queue v12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Node.js chamada *queues-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Mude para o diretório *de filas-quickstart-v12 recém-criado.*

    ```console
    cd queues-quickstart-v12
    ```

1. Crie um novo ficheiro de texto chamado *package.jsligado*. Este ficheiro define o projeto Node.js. Guarde este ficheiro no diretório *de entradas rápidas-v12.* Aqui está o conteúdo do ficheiro:

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

    Pode colocar o seu próprio nome no `author` campo, se quiser.

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório *de filas-quickstart-v12,* instale a biblioteca do cliente de armazenamento Azure Queue para o pacote JavaScript utilizando o `npm install` comando.

```console
npm install
```

 Este comando lê o *package.jsno* ficheiro e instala a biblioteca de clientes de armazenamento Azure Queue v12 para o pacote JavaScript e todas as bibliotecas de que depende.

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra mais um novo ficheiro de texto no seu editor de código
1. Adicionar `require` chamadas para carregar módulos Azure e Node.js
1. Crie a estrutura para o programa, incluindo o manuseamento de exceções muito básico

    Aqui está o código:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Guarde o novo ficheiro à medida *quequeues-quickstart-v12.js* no diretório *de filas-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos. O armazenamento de fila oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama de arquitetura de armazenamento de fila](./media/storage-queues-introduction/queue1.png)

Utilize as seguintes classes JavaScript para interagir com estes recursos:

- [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` Permite-lhe gerir todas as filas na sua conta de armazenamento.
- [QueueClient](/javascript/api/@azure/storage-queue/queueclient): A `QueueClient` classe permite-lhe gerir e manipular uma fila individual e as suas mensagens.
- [FilaMessage](/javascript/api/@azure/storage-queue/queuemessage): A `QueueMessage` classe representa os objetos individuais devolvidos ao chamar [as caixas de correio numa](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) fila.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código de exemplo mostram-lhe como fazer as seguintes ações com a biblioteca de clientes de armazenamento de fila Azure para JavaScript:

- [Obter a cadeia de ligação](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicione mensagens a uma fila](#add-messages-to-a-queue)
- [Espreite as mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualize uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber mensagens de uma fila](#receive-messages-from-a-queue)
- [Apagar mensagens de uma fila](#delete-messages-from-a-queue)
- [Eliminar uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiente criada na secção de cadeia de [ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro da `main` função:

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

Decida um nome para a nova fila. O código abaixo anexa um valor UUID ao nome da fila para garantir que é único.

> [!IMPORTANT]
> Os nomes da fila só podem conter letras minúsculas, números e hífens, e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre o nome de filas, consulte [As Filas de Nomeação e Os Metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie um exemplo da classe [QueueClient.](/javascript/api/@azure/storage-queue/queueclient) Em seguida, ligue para o método [de criação](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) para criar a fila na sua conta de armazenamento.

Adicione este código ao fim da `main` função:

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

### <a name="add-messages-to-a-queue"></a>Adicione mensagens a uma fila

O seguinte corte de código adiciona mensagens à fila, chamando o método [de envio de mensagens.](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) Também salva a [FilaMessage](/javascript/api/@azure/storage-queue/queuemessage) devolvida da terceira `sendMessage` chamada. O devolvido `sendMessageResponse` é utilizado para atualizar o conteúdo da mensagem mais tarde no programa.

Adicione este código ao fim da `main` função:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Espreite as mensagens em uma fila

Espreite as mensagens na fila chamando o método [peekMessages.](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) O `peekMessages` método recupera uma ou mais mensagens da parte da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao fim da `main` função:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Atualize uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [de mensagens de atualização.](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) O `updateMessage` método pode alterar o tempo limite de visibilidade e o conteúdo de uma mensagem. O conteúdo da mensagem deve ser uma cadeia codificada UTF-8 que tem até 64 KB de tamanho. Juntamente com o novo conteúdo, passe dentro `messageId` e a partir da resposta que foi `popReceipt` guardada mais cedo no código. As `sendMessageResponse` propriedades identificam a mensagem a atualizar.

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

Descarregue mensagens previamente adicionadas chamando o método [de receber mensagens.](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) No `numberOfMessages` campo, passe o número máximo de mensagens a receber para esta chamada.

Adicione este código ao fim da `main` função:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Apagar mensagens de uma fila

Apague as mensagens da fila depois de recebidas e processadas. Neste caso, o processamento é apenas exibindo a mensagem na consola.

Eliminar mensagens chamando o método [deleteMessage.](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) Quaisquer mensagens não explicitamente apagadas acabarão por se tornar visíveis na fila novamente para outra oportunidade de as processar.

Adicione este código ao fim da `main` função:

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

O código seguinte limpa os recursos que a app criou eliminando a fila utilizando o método [de eliminação.](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)

Adicione este código ao fim da `main` função e guarde o ficheiro:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria e adiciona três mensagens a uma fila Azure. O código lista as mensagens na fila e, em seguida, recupera-as e apaga-as, antes de finalmente apagar a fila.

Na janela da consola, navegue para o diretório que contém o ficheiro *queues-quickstart-v12.js* e, em seguida, execute o seguinte `node` comando para executar a aplicação.

```console
node queues-quickstart-v12.js
```

A saída da app é semelhante ao seguinte exemplo:

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

Passe pelo código no seu depurador e verifique o seu [portal Azure](https://portal.azure.com) durante todo o processo. Verifique a sua conta de armazenamento para verificar se as mensagens na fila são criadas e eliminadas.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma fila e a adicionar-lhe mensagens utilizando o código JavaScript. Depois aprendeu a espreitar, a recuperar e a apagar mensagens. Finalmente, aprendeu a apagar uma fila de mensagens.

Para tutoriais, amostras, arranques rápidos e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para documentação JavaScript](/azure/developer/javascript/)

- Para saber mais, consulte a biblioteca de clientes da [Fila de Armazenamento Azure para o JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- Para ver mais aplicativos de amostra de armazenamento da Azure Queue, continue até a [biblioteca de clientes de armazenamento Azure Queue v12 Amostras JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
