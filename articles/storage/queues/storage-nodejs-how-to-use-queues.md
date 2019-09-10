---
title: Usar o armazenamento de filas do Azure do node. js – armazenamento do Azure
description: Saiba como usar o serviço Fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Exemplos escritos em node. js.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 83aecc9c05fec5869a82930f2d687ac907981c00
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858676"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Como utilizar o Armazenamento de filas do Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar cenários comuns usando o Microsoft Azure serviço Fila. Os exemplos são escritos usando a API do node. js. Os cenários cobertos incluem **Inserir**, **inspecionar**, **obter**e **excluir** mensagens da fila, bem como **criar e excluir filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo node. js
Crie um aplicativo node. js em branco. Para obter instruções sobre como criar um aplicativo node. js, consulte [criar um aplicativo Web node. js no serviço Azure app](../../app-service/app-service-web-get-started-nodejs.md), [criar e implantar um aplicativo node. js em um serviço de nuvem do Azure usando o](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) Windows PowerShell ou [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento
Para usar o armazenamento do Azure, você precisa do SDK do armazenamento do Azure para node. js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o Gerenciador de pacotes de nó (NPM) para obter o pacote
1. Use uma interface de linha de comando, como **PowerShell** (Windows,) **terminal** (Mac,) ou **bash** (Unix), navegue até a pasta em que você criou o aplicativo de exemplo.
2. Escreva **npm install azure-storage** na janela de comandos. Os resultados do comando são semelhantes ao seguinte exemplo.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Você pode executar manualmente o comando **ls** para verificar se uma **pasta\_node modules** foi criada. Nessa pasta, encontrará o pacote **azure-storage**, que contém as bibliotecas de que precisa para aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Usando o bloco de notas ou outro editor de texto, adicione o seguinte ao início do arquivo **Server. js** do aplicativo em que você pretende usar o armazenamento:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure
O módulo do Azure lerá as variáveis de ambiente\_conta\_de armazenamento do\_Azure\_e\_chave de acesso de\_armazenamento\_do Azure ou conexãodearmazenamentodoAzure\_ Cadeia de caracteres para as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar **createQueueService**.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código a seguir cria um objeto **QueueService** , que permite que você trabalhe com filas.

```javascript
var queueSvc = azure.createQueueService();
```

Use o método **createQueueIfNotExists** , que retorna a fila especificada se ela já existe ou cria uma nova fila com o nome especificado, caso ela ainda não exista.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila for criada, `result.created` será verdadeira. Se a fila existir, `result.created` será false.

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas às operações executadas usando **QueueService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de fazer seu pré-processamento nas opções de solicitação, o método precisa chamar "Next" passando um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e depois de processar o returnobject (a resposta da solicitação para o servidor), o retorno de chamada precisará invocar Next se ele existir para continuar processando outros filtros ou simplesmente invocar finalCallback de outra forma para finalizar o serviço invocação.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um objeto **QueueService** que usa o **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila, use o método **CreateMessage** para criar uma nova mensagem e adicioná-la à fila.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Inspecionar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peekMessages** . Por padrão, o **peekMessages** exibe uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

O `result` contém a mensagem.

> [!NOTE]
> O uso de **peekMessages** quando não há mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover a próxima mensagem da fila
O processamento de uma mensagem é um processo de duas etapas:

1. Remover a mensagem da fila.
2. Exclua a mensagem.

Para remover uma mensagem da fila, use **GetMessages**. Isso torna as mensagens invisíveis na fila, portanto, nenhum outro cliente pode processá-las. Depois que o aplicativo tiver processado uma mensagem, chame **deleteMessage** para excluí-la da fila. O exemplo a seguir obtém uma mensagem e, em seguida, a exclui:

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

> [!NOTE]
> Por padrão, uma mensagem é ocultada apenas por 30 segundos, após a qual ela fica visível para outros clientes. Você pode especificar um valor diferente usando `options.visibilityTimeout` o com **GetMessages**.
> 
> [!NOTE]
> O uso de **GetMessages** quando não há mensagens na fila não retornará um erro, no entanto, nenhuma mensagem será retornada.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Você pode alterar o conteúdo de uma mensagem in-loco na fila usando **updateMessage**. O exemplo a seguir atualiza o texto de uma mensagem:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para o enfileiramento de mensagens
Há duas maneiras de personalizar a recuperação de mensagens de uma fila:

* `options.numOfMessages`-Recuperar um lote de mensagens (até 32.)
* `options.visibilityTimeout`-Definir um tempo limite de invisibilidade mais longo ou menor.

O exemplo a seguir usa o método **GetMessages** para obter 15 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens retornadas por esse método.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
O **getQueueMetadata** retorna metadados sobre a fila, incluindo o número aproximado de mensagens aguardando na fila.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Como: Listar filas
Para recuperar uma lista de filas, use **listQueuesSegmented**. Para recuperar uma lista filtrada por um prefixo específico, use **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas não puderem ser `result.continuationToken` retornadas, poderão ser usadas como o primeiro parâmetro de **listQueuesSegmented** ou o segundo parâmetro de **listQueuesSegmentedWithPrefix** para recuperar mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o método **deleteQueue** no objeto Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem excluí-las, use **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: Utilizar Assinaturas de Acesso Partilhado
As SAS (assinaturas de acesso compartilhado) são uma maneira segura de fornecer acesso granular a filas sem fornecer o nome ou as chaves da conta de armazenamento. As SAS são frequentemente usadas para fornecer acesso limitado às suas filas, como permitir que um aplicativo móvel envie mensagens.

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma SAS usando o **generateSharedAccessSignature** da **QueueService**e a fornece a um aplicativo não confiável ou semiconfiável. Por exemplo, um aplicativo móvel. A SAS é gerada utilizando uma política, que descreve as datas de início e de fim durante as quais a SAS é válida, bem como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o proprietário da SAS adicione mensagens à fila e expire em 100 minutos após a hora em que é criada.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Observe que as informações do host também devem ser fornecidas, pois são necessárias quando o detentor da SAS tenta acessar a fila.

Em seguida, o aplicativo cliente usa a SAS com **QueueServiceWithSAS** para executar operações na fila. O exemplo a seguir conecta-se à fila e cria uma mensagem.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Como a SAS foi gerada com a adição de acesso, se houvesse uma tentativa de ler, atualizar ou excluir mensagens, um erro será retornado.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Também pode utilizar uma Lista de Controlo de Acesso (ACL) para definir a política de acesso para uma SAS. Isso é útil se você deseja permitir que vários clientes acessem a fila, mas fornecem políticas de acesso diferentes para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo a seguir define duas políticas; um para ' Usuário1 ' e outro para ' Usuário2 ':

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo a seguir obtém a ACL atual para **MyQueue**e, em seguida, adiciona as novas políticas usando **setQueueAcl**. Esta abordagem permite:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Depois que a ACL tiver sido definida, você poderá criar uma SAS com base na ID de uma política. O exemplo seguinte cria uma nova SAS para "user2":

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Próximos Passos
Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

* Aceda ao [Blogue da Equipa do Storage do Azure][Azure Storage Team Blog].
* Visite o repositório [SDK do armazenamento do Azure para nó][Azure Storage SDK for Node] no github.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Criar um aplicativo Web node. js no serviço Azure App](../../app-service/app-service-web-get-started-nodejs.md)

[Compilar e implementar uma aplicação Node.js num Serviço Cloud do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
