---
title: Utilize o armazenamento de fila Azure a partir do Nó.js - Armazenamento Azure
description: Aprenda a utilizar o serviço De Fila Azure para criar e apagar filas, e inserir, obter e apagar mensagens. Amostras escritas no Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060924"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Utilize o Serviço de Fila Azure para criar e apagar filas a partir de Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como realizar cenários comuns utilizando o serviço microsoft Azure Queue. As amostras são escritas usando a API node.js. Os cenários abordados incluem **a inserção,** **espreitar,** **receber**e **apagar** mensagens de fila, bem como criar e **apagar filas.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação node.js em branco. Para obter instruções para criar uma aplicação Node.js, consulte [Create a Node.js web app no Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [Construa e implemente uma aplicação Node.js para um Serviço De Nuvem Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) utilizando o Windows PowerShell, ou [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento Azure, necessita do Azure Storage SDK para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use O Gestor de Pacotes do Nó (NPM) para obter o pacote
1. Utilize uma interface de linha de comando, como **o PowerShell** (Windows,) **Terminal** (Mac,) ou **Bash** (Unix), navegar para a pasta onde criou a sua aplicação de amostra.
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

3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta de **\_módulos** de nó. Nessa pasta, encontrará o pacote **azure-storage**, que contém as bibliotecas de que precisa para aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Utilizando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao ficheiro **'server.js'** da aplicação onde pretende utilizar o armazenamento:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento azure
O módulo azul irá ler as\_variáveis ambientais AZURE\_STORAGE ACCOUNT e AZURE\_STORAGE\_\_ACCESS KEY, ou AZURE\_STORAGE\_CONNECTION\_STRING para informações necessárias para se ligar à sua conta de armazenamento Azure. Se estas variáveis ambientais não forem definidas, deve especificar as informações da conta ao ligar **para criar serviçode fila**.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O seguinte código cria um objeto **QueueService,** que lhe permite trabalhar com filas.

```javascript
var queueSvc = azure.createQueueService();
```

Utilize o método **CreateQueueIfNotExists,** que devolve a fila especificada se já existe ou cria uma nova fila com o nome especificado se ainda não existir.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila `result.created` for criada, é verdade. Se a fila `result.created` existe, é falso.

### <a name="filters"></a>Filtros
As operações de filtragem opcionais podem ser aplicadas às operações realizadas através do **QueueService**. As operações de filtragem podem incluir a exploração madeireira, a retentar automaticamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de fazer o seu pré-processamento sobre as opções de pedido, o método precisa de chamar "seguinte" passando uma chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após o processamento do ReturnObject (a resposta do pedido ao servidor), o backback precisa de invocar a seguir se existir para continuar a processar outros filtros ou simplesmente invocar o Backback final de outra forma para acabar com a invocação do serviço.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um objeto **QueueService** que utiliza o **ExponencialRetryPolicyFilter:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem numa fila
Para inserir uma mensagem numa fila, utilize o método **createMessage** para criar uma nova mensagem e adicioná-la à fila.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem
Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando o método **peekMessages.** Por padrão, **peekMessages** espreita uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

A `result` mensagem contém.

> [!NOTE]
> Utilizar **mensagens de espreitar** quando não houver mensagens na fila não devolverá um erro, no entanto nenhuma mensagem será devolvida.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Como: Defilar a Próxima Mensagem
Processar uma mensagem é um processo em duas fases:

1. Desfila a mensagem.
2. Apague a mensagem.

Para desfilar uma mensagem, use **getMessages**. Isto torna as mensagens invisíveis na fila, para que nenhum outro cliente as possa processar. Uma vez que a sua aplicação tenha processado uma mensagem, ligue para **apagar Mensagem** para apagá-la da fila. O exemplo seguinte recebe uma mensagem e depois apaga::

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
> Por padrão, uma mensagem só é escondida durante 30 segundos, após a qual é visível para outros clientes. Pode especificar um valor `options.visibilityTimeout` diferente utilizando com **getMessages**.
> 
> [!NOTE]
> A utilização **de mensagens getMessages** quando não há mensagens na fila não devolverá um erro, no entanto nenhuma mensagem será devolvida.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar o conteúdo de uma mensagem no lugar na fila utilizando a **actualizaçãoMensagem**. O exemplo seguinte atualiza o texto de uma mensagem:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para descodar mensagens
Existem duas formas de personalizar a recuperação de mensagens a partir de uma fila:

* `options.numOfMessages`- Recuperar um lote de mensagens (até 32.)
* `options.visibilityTimeout`- Detete um intervalo de invisibilidade mais longo ou mais curto.

O exemplo que se segue utiliza o método **getMessages** para obter 15 mensagens numa chamada. Em seguida, processa cada mensagem usando um para loop. Também define o tempo de invisibilidade para cinco minutos para todas as mensagens devolvidas por este método.

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
O **getQueueMetadata** devolve metadados sobre a fila, incluindo o número aproximado de mensagens à espera na fila.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Como: Lista r' seleções
Para recuperar uma lista de filas, utilize **listaSSegmentadas**. Para recuperar uma lista filtrada por um prefixo específico, utilize **listaSSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas `result.continuationToken` não puderem ser devolvidas, pode ser usada como o primeiro parâmetro das **listQueuesSegmented** ou o segundo parâmetro de **listQueuesSegmentedWithPrefix** para obter mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila
Para eliminar uma fila e todas as mensagens contidas na seleção, ligue para o método **deleteQueue** no objeto de fila.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem a apagar, utilize **mensagens claras**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: Trabalhar com assinaturas de acesso partilhado
As Assinaturas de Acesso Partilhado (SAS) são uma forma segura de fornecer acesso granular às filas sem fornecer o nome ou as chaves da sua conta de armazenamento. As SAS são frequentemente utilizadas para fornecer acesso limitado às suas filas, tais como permitir que uma aplicação móvel envie mensagens.

Uma aplicação fidedigna, como um serviço baseado na nuvem, gera um SAS utilizando a **geraçãoSharedAccessSignature** do **QueueService,** e fornece-a a uma aplicação não confiável ou semi-fidedigna. Por exemplo, uma aplicação móvel. A SAS é gerada utilizando uma política, que descreve as datas de início e de fim durante as quais a SAS é válida, bem como o nível de acesso concedido ao titular da SAS.

O exemplo seguinte gera uma nova política de acesso partilhado que permitirá ao titular da SAS adicionar mensagens à fila, e expira 100 minutos após a sua criação.

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

Note que as informações do anfitrião também devem ser fornecidas, como é necessário quando o titular da SAS tenta aceder à fila.

A aplicação do cliente utiliza então o SAS com **QueueServiceWithSAS** para realizar operações contra a fila. O exemplo seguinte liga-se à fila e cria uma mensagem.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Uma vez que o SAS foi gerado com acesso ao add, se fosse feita uma tentativa de ler, atualizar ou apagar mensagens, um erro seria devolvido.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Também pode utilizar uma Lista de Controlo de Acesso (ACL) para definir a política de acesso para uma SAS. Isto é útil se desejar permitir que vários clientes acedam à fila, mas que proporcione diferentes políticas de acesso para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo que se segue define duas políticas; uma para 'user1' e outra para 'user2':

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

O exemplo seguinte obtém o ACL atual para **a myqueue,** em seguida, adiciona as novas políticas usando **setQueueAcl**. Esta abordagem permite:

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

Uma vez definida a ACL, pode criar um SAS com base no ID para uma apólice. O exemplo seguinte cria uma nova SAS para "user2":

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

* Aceda ao [Blogue da Equipa do Storage do Azure][Azure Storage Team Blog].
* Visite o [Azure Storage SDK para][Azure Storage SDK for Node] repositório de nó no GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Criar uma aplicação Web Node.js no App Service do Azure](../../app-service/app-service-web-get-started-nodejs.md)

[Compilar e implementar uma aplicação Node.js num Serviço em Nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
