---
title: Como utilizar o armazenamento da Azure Queue a partir de Node.js - Azure Storage
description: Aprenda a utilizar o serviço Azure Queue para criar e apagar filas. Aprenda a inserir, obter e apagar mensagens usando Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: c5a9fb1a179164d24c84213762ee7e2332a1aa25
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345946"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Como utilizar o armazenamento da Azure Queue a partir de Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como realizar cenários comuns usando o serviço Microsoft Azure Queue. As amostras são escritas com a API Node.js. Os cenários abrangidos incluem inserir, espreitar, receber e apagar mensagens de fila. Também aprenda a criar e apagar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

Para criar uma aplicação de Node.js em branco, consulte [criar uma aplicação web Node.js no Azure App Service][Create a Node.js web app in Azure App Service], [Construa e implemente uma aplicação Node.js para um Serviço de Nuvem Azure][Build and deploy a Node.js application to an Azure Cloud Service] utilizando o Windows PowerShell ou [Código de Estúdio Visual][Visual Studio Code].

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento

A [biblioteca de clientes Azure Storage para JavaScript][Azure Storage client library for JavaScript] inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços DE REST de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gestor de Pacotes de Nó (NPM) para obter o pacote

1. Utilize uma interface de linha de comando como PowerShell (Windows), Terminal (Mac) ou Bash (Unix), navegue para a pasta onde criou a sua aplicação de amostra.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Tipo **npm instalar \@ a azul/fila de armazenamento** na janela de comando.

1. Verifique se foi criada uma pasta **de módulos de \_ nó.** Dentro dessa pasta encontrará o pacote **\@ azure/storage-queue,** que contém a biblioteca do cliente que precisa para aceder ao armazenamento.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Escreva **npm install azure-storage** na janela de comandos.

1. Verifique se foi criada uma pasta **de módulos de \_ nó.** Dentro dessa pasta, encontrará o pacote **de armazenamento azul,** que contém as bibliotecas que precisa para aceder ao armazenamento.

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

O código seguinte obtém o valor de uma variável ambiental chamada `AZURE_STORAGE_CONNECTION_STRING` e usa-o para criar um objeto [QueueServiceClient.](/javascript/api/@azure/storage-queue/queueserviceclient) O objeto **QueueServiceClient** é então utilizado para criar um objeto [QueueClient.](/javascript/api/@azure/storage-queue/queueclient) O objeto **QueueClient** permite-lhe trabalhar com uma fila específica.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Se a fila já existe, uma exceção é lançada.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O módulo Azure irá ler as variáveis ambientais `AZURE_STORAGE_ACCOUNT` `AZURE_STORAGE_ACCESS_KEY` e, ou `AZURE_STORAGE_CONNECTION_STRING` para informações necessárias para ligar à sua conta de armazenamento Azure. Se estas variáveis ambientais não estiverem definidas, deve especificar as informações da conta ao ligar para **o CreateQueueService**.

O código a seguir cria um objeto **QueueService,** que lhe permite trabalhar com filas.

```javascript
var queueSvc = azure.createQueueService();
```

Ligue para o método **createQueueIfNotExists** para criar uma nova fila com o nome especificado ou devolva a fila se já existir.

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

Para adicionar uma mensagem a uma fila, ligue para o método [de envio de mensagens.](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para inserir uma mensagem numa fila, ligue para o método **createMessage** para criar uma nova mensagem e adicioná-la à fila.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Como espreitar a próxima mensagem

Pode espreitar as mensagens na fila sem as retirar da fila, chamando o método **peekMessages.**

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Por padrão, [as mensagens](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) espreitam uma única mensagem. O exemplo a seguir espreita as cinco primeiras mensagens na fila. Se forem visíveis menos de cinco mensagens, apenas as mensagens visíveis são devolvidas.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Por padrão, **as mensagens** espreitam uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Contém `result` a mensagem.

---

Chamar **mensagens** quando não há mensagens na fila não vai responder a um erro. No entanto, não são devolvidas mensagens.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

O exemplo a seguir atualiza o texto de uma mensagem.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Altere o conteúdo de uma mensagem no lugar na fila chamando [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-).

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Altere o conteúdo de uma mensagem no lugar na fila chamando **updateMessage**.

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

Para obter uma mensagem, ligue para o método [de receber mensagens.](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) Esta chamada torna as mensagens invisíveis na fila, para que nenhum outro cliente possa processá-las. Uma vez que a sua aplicação tenha processado uma mensagem, ligue [para apagar](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) a sua parte da fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Por defeito, uma mensagem só é ocultada durante 30 segundos. Após 30 segundos é visível para outros clientes. Pode especificar um valor diferente definindo [opções.visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) quando ligar **para receber caixas .**

Receber **mensagens** quando não há mensagens na fila não retorna um erro. No entanto, nenhuma mensagem será devolvida.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para obter uma mensagem, ligue para o método **getMessages.** Esta chamada torna as mensagens invisíveis na fila, para que nenhum outro cliente possa processá-las. Uma vez que a sua aplicação tenha processado uma mensagem, ligue **para apagar** a sua parte da fila.

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

Por defeito, uma mensagem só é ocultada durante 30 segundos. Após 30 segundos é visível para outros clientes. Pode especificar um valor diferente utilizando `options.visibilityTimeout` com as caixas de **correio.**

A utilização **de mensagens get** Quando não há mensagens na fila não devolve um erro. No entanto, nenhuma mensagem será devolvida.

---

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desescodução de mensagens

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Há duas formas de personalizar a recuperação de mensagens a partir de uma fila:

- [opções.numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) - Recuperar um lote de mensagens (até 32.)
- [opções.visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) - Desemti o tempo limite de invisibilidade mais longo ou curto.

O exemplo a seguir utiliza o método **de receber mensagens** para receber cinco mensagens numa chamada. Em seguida, processa cada mensagem usando um `for` loop. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens devolvidas por este método.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Há duas formas de personalizar a recuperação de mensagens a partir de uma fila:

- `options.numOfMessages` - Recuperar um lote de mensagens (até 32.)
- `options.visibilityTimeout` - Desemti o tempo limite de invisibilidade mais longo ou curto.

O exemplo a seguir utiliza o método **getMessages** para obter 15 mensagens numa chamada. Em seguida, processa cada mensagem usando um `for` loop. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens devolvidas por este método.

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

O método [getProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) devolve metadados sobre a fila, incluindo o número aproximado de mensagens à espera na fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O método **getQueueMetadata** devolve metadados sobre a fila, incluindo o número aproximado de mensagens à espera na fila.

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

Para obter uma lista de filas, ligue para [QueueServiceClient.listQueues](). Para recuperar uma lista filtrada por um prefixo específico, desacorra [opções.prefixo](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) na sua chamada para **listaSQuues**.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para obter uma lista de filas, utilize **listQueuesSegmented**. Para obter uma lista filtrada por um prefixo específico, utilize **a listaQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas não puderem ser devolvidas, passe `result.continuationToken` como o primeiro parâmetro da **listQueuesSegmented** ou o segundo parâmetro da **listQueuesSegmentedWithPrefix** para obter mais resultados.

---

## <a name="how-to-delete-a-queue"></a>Como apagar uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para eliminar uma fila e todas as mensagens contidas nela, ligue para o método [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) no objeto **QueueClient.**

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Para limpar todas as mensagens de uma fila sem a apagar, chame [as mensagens clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-).

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para eliminar uma fila e todas as mensagens contidas na sua parte, ligue para o método **deleteQueue** no objeto da fila.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem a apagar, chame **as mensagens clearMessages**.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

- Visite o [Azure Storage Team Blog][Azure Storage Team Blog] para saber quais as novidades
- Visite a biblioteca de [clientes do Azure Storage para][Azure Storage client library for JavaScript] o repositório JavaScript no GitHub

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
