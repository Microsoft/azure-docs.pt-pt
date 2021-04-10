---
title: Como utilizar o armazenamento da fila Azure a partir de Node.js - Azure Storage
description: Aprenda a utilizar o Azure Queue Storage para criar e apagar filas. Aprenda a inserir, obter e apagar mensagens usando Node.js.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 161000f2860a1153424a628a2b303f3717f828da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275945"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Como utilizar o armazenamento da fila Azure a partir de Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Descrição Geral

Este guia mostra-lhe como realizar cenários comuns usando o Azure Queue Storage. As amostras são escritas com a API Node.js. Os cenários abrangidos incluem inserir, espreitar, receber e apagar mensagens de fila. Também aprenda a criar e apagar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Para criar uma aplicação de Node.js em branco, consulte [criar uma aplicação web Node.js no Azure App Service](../../app-service/quickstart-nodejs.md), [construa e implemente uma aplicação Node.js para a Azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) utilizando o PowerShell ou o Visual Studio [Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento

A [biblioteca de clientes Azure Storage para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços DE REST de armazenamento.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o gestor de pacotes no nó (npm) para obter o pacote

1. Utilize uma interface de linha de comando como PowerShell (Windows), Terminal (Mac) ou Bash (Unix), navegue para a pasta onde criou a sua aplicação de amostra.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Digite `npm install @azure/storage-queue` a janela de comando.

1. Verifique se foi criada uma `node_modules` pasta. Dentro dessa pasta encontrará o `@azure/storage-queue` pacote, que contém a biblioteca do cliente que precisa para aceder ao armazenamento.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Digite `npm install azure-storage` a janela de comando.

1. Verifique se foi criada uma `node_modules` pasta. Dentro dessa pasta, encontrará o `azure-storage` pacote, que contém as bibliotecas que precisa para aceder ao armazenamento.

---

### <a name="import-the-package"></a>Importar o pacote

Utilizando o seu editor de código, adicione o seguinte ao topo do ficheiro JavaScript onde pretende utilizar as filas.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Como criar uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O seguinte código obtém o valor de uma variável ambiental chamada `AZURE_STORAGE_CONNECTION_STRING` e usa-o para criar um [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) objeto. Este objeto é então usado para criar um [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) objeto que lhe permite trabalhar com uma fila específica.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Se a fila já existe, uma exceção é lançada.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O módulo Azure irá ler as variáveis ambientais `AZURE_STORAGE_ACCOUNT` `AZURE_STORAGE_ACCESS_KEY` e, ou `AZURE_STORAGE_CONNECTION_STRING` para informações necessárias para ligar à sua conta de Armazenamento Azure. Se estas variáveis ambientais não estiverem definidas, deve especificar as informações da conta ao ligar `createQueueService` .

O código a seguir cria um `QueueService` objeto, que lhe permite trabalhar com filas.

```javascript
var queueSvc = azure.createQueueService();
```

Ligue para o `createQueueIfNotExists` método para criar uma nova fila com o nome especificado ou devolva a fila se já existir.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila for criada, `result.created` é verdade. Se a fila existe, `result.created` é falso.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem numa fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para adicionar uma mensagem a uma fila, ligue para o [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) método.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para inserir uma mensagem numa fila, ligue para o `createMessage` método para criar uma nova mensagem e adicione-a à fila.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Como espreitar a próxima mensagem

Pode espreitar as mensagens na fila sem as retirar da fila, chamando o `peekMessages` método.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Por padrão, [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) espreita uma única mensagem. O exemplo a seguir espreita as cinco primeiras mensagens na fila. Se forem visíveis menos de cinco mensagens, apenas as mensagens visíveis são devolvidas.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Por padrão, `peekMessages` espreita uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Contém `result` a mensagem.

---

Ligar `peekMessages` quando não há mensagens na fila não devolve um erro. No entanto, não são devolvidas mensagens.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

O exemplo a seguir atualiza o texto de uma mensagem.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Altere o conteúdo de uma mensagem no lugar na fila ligando [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Altere o conteúdo de uma mensagem no lugar na fila ligando `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Como descoduar uma mensagem

Descoduar uma mensagem é um processo em duas fases:

1. Pegue a mensagem.

1. Apague a mensagem.

O exemplo a seguir recebe uma mensagem e depois apaga-a.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para obter uma mensagem, ligue para o [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) método. Esta chamada torna as mensagens invisíveis na fila, para que nenhum outro cliente possa processá-las. Uma vez que a sua aplicação tenha processado uma mensagem, ligue [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) para a eliminar da fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Por defeito, uma mensagem só é ocultada durante 30 segundos. Após 30 segundos é visível para outros clientes. Pode especificar um valor diferente definindo [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) quando ligar `receiveMessages` .

Ligar `receiveMessages` quando não há mensagens na fila não devolve um erro. No entanto, nenhuma mensagem será devolvida.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para obter uma mensagem, ligue para o `getMessages` método. Esta chamada torna as mensagens invisíveis na fila, para que nenhum outro cliente possa processá-las. Uma vez que a sua aplicação tenha processado uma mensagem, ligue `deleteMessage` para a eliminar da fila.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Por defeito, uma mensagem só é ocultada durante 30 segundos. Após 30 segundos é visível para outros clientes. Pode especificar um valor diferente utilizando `options.visibilityTimeout` `getMessages` com .

A utilização `getMessages` quando não há mensagens na fila não devolve um erro. No entanto, nenhuma mensagem será devolvida.

---

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desescodução de mensagens

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Há duas formas de personalizar a recuperação de mensagens a partir de uma fila:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Recupere um lote de mensagens (até 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Desemti o tempo limite de invisibilidade mais longo ou curto.

O exemplo a seguir utiliza o `receiveMessages` método para receber cinco mensagens numa chamada. Em seguida, processa cada mensagem usando um `for` loop. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens devolvidas por este método.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Há duas formas de personalizar a recuperação de mensagens a partir de uma fila:

- `options.numOfMessages`: Recupere um lote de mensagens (até 32).
- `options.visibilityTimeout`: Desemti o tempo limite de invisibilidade mais longo ou curto.

O exemplo a seguir utiliza o `getMessages` método para obter 15 mensagens numa chamada. Em seguida, processa cada mensagem usando um `for` loop. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens devolvidas por este método.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Como obter o comprimento da fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) método devolve metadados sobre a fila, incluindo o número aproximado de mensagens à espera na fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O `getQueueMetadata` método devolve metadados sobre a fila, incluindo o número aproximado de mensagens à espera na fila.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Como listar filas

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para recuperar uma lista de filas, [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) ligue. Para recuperar uma lista filtrada por um prefixo específico, desacorra [opções.prefixo](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) na sua chamada para `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para recuperar uma lista de filas, use `listQueuesSegmented` . Para obter uma lista filtrada por um prefixo específico, utilize `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas não puderem ser devolvidas, passe `result.continuationToken` como o primeiro parâmetro ou o segundo parâmetro de obter mais `listQueuesSegmented` `listQueuesSegmentedWithPrefix` resultados.

---

## <a name="how-to-delete-a-queue"></a>Como apagar uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para eliminar uma fila e todas as mensagens contidas, ligue para o [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) método do `QueueClient` objeto.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Para limpar todas as mensagens de uma fila sem a apagar, [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) ligue.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para eliminar uma fila e todas as mensagens contidas, ligue para o `deleteQueue` método no objeto da fila.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem a apagar, `clearMessages` ligue.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico do Armazenamento de Fila, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

- Visite o blog da equipa de [armazenamento Azure](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) para saber quais as novidades
- Visite a biblioteca de [clientes do Azure Storage para](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) o repositório JavaScript no GitHub
