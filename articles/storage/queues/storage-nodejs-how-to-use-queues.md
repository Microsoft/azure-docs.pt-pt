---
title: Como utilizar o armazenamento de filas do node. js | Documentos da Microsoft
description: Saiba como utilizar o serviço de fila do Azure para criar e eliminar filas, inserir, obter e eliminar mensagens. Amostras de escrita em node. js.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 31f0be804085643d048a35dd61da9156f291f7da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006459"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Como utilizar o Armazenamento de filas do Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar tarefas comuns com o serviço de fila do Microsoft Azure. Os exemplos são escritos usando a API de node. js. Os cenários abrangidos incluem **inserindo**, **observação**, **introdução**, e **a eliminar** fila de mensagens, que  **criar e eliminar filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação node. js
Crie uma aplicação node. js em branco. Para obter instruções sobre como criar uma aplicação node. js, consulte [criar uma aplicação web do node. js no App Service do Azure](../../app-service/app-service-web-get-started-nodejs.md), [compilar e implementar uma aplicação node. js num serviço Cloud do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) com o Windows PowerShell ou [ Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para o armazenamento de acesso
Para utilizar o armazenamento do Azure, terá do SDK de armazenamento do Azure para node. js, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços de REST de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o nó Package Manager (NPM) para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix), navegue para a pasta onde criou o exemplo de aplicação.
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

3. Pode executar manualmente a **ls** comando para verificar se um **nó\_módulos** pasta foi criada. Nessa pasta, encontrará o pacote **azure-storage**, que contém as bibliotecas de que precisa para aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Usando o bloco de notas ou outro editor de texto, adicione o seguinte na parte superior a **Server. js** arquivo do aplicativo em que pretende usar o armazenamento:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente do AZURE\_armazenamento\_conta e do AZURE\_armazenamento\_acesso\_chave ou o AZURE\_armazenamento\_ligação\_ A cadeia de caracteres de informações necessárias para ligar à sua conta de armazenamento do Azure. Se não forem definidas estas variáveis de ambiente, tem de especificar as informações da conta, ao chamar **createQueueService**.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código seguinte cria um **QueueService** objeto, que lhe permite trabalhar com as filas.

```javascript
var queueSvc = azure.createQueueService();
```

Utilize o **createQueueIfNotExists** método, que retorna a fila especificada, se já existe ou cria uma nova fila com o nome especificado, caso ainda não exista.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila é criada, `result.created` é verdadeiro. Se a fila existe, `result.created` é false.

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **QueueService**. Operações de filtragem pode incluir o Registro em log, tentar estabelecer novamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de fazer o processamento prévio sobre as opções de pedido, o método precisa chamar "seguinte", passando um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Esse retorno de chamada e depois de processar o returnObject (a resposta do pedido para o servidor), tem do retorno de chamada invocar em seguida, se existir para continuar a processar outros filtros ou simplesmente invocar finalCallback caso contrário, para terminar o serviço invocação.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um **QueueService** objeto que usa o **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem numa fila
Para introduzir uma mensagem numa fila, utilize o **createMessage** método para criar uma nova mensagem e adicioná-lo para a fila.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualizar a mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peekMessages** método. Por predefinição, **peekMessages** observa uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

O `result` contém a mensagem.

> [!NOTE]
> Usando **peekMessages** quando não existirem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem vai ser devolvida.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover da fila a mensagem seguinte
Processar uma mensagem é um processo de duas etapas:

1. Remover da fila a mensagem.
2. Elimine a mensagem.

Para remover uma mensagem da fila, utilize **getMessages**. Desta forma, as mensagens invisível na fila, para que não existem outros clientes podem processá-los. Depois de seu aplicativo processou uma mensagem, chame **deleteMessage** eliminá-lo da fila. O exemplo seguinte obtém uma mensagem, em seguida, elimina-a:

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
> Por predefinição, uma mensagem apenas está oculta durante 30 segundos, após o qual está visível para outros clientes. Pode especificar um valor diferente utilizando `options.visibilityTimeout` com **getMessages**.
> 
> [!NOTE]
> Usando **getMessages** quando não existirem mensagens na fila não retornará um erro, no entanto, nenhuma mensagem vai ser devolvida.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar o conteúdo de uma mensagem no local na fila utilizando **updateMessage**. O exemplo seguinte atualiza o texto de uma mensagem:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para remoção da fila de mensagens
Existem duas formas, pode personalizar a obtenção de mensagens de uma fila:

* `options.numOfMessages` -Obter um lote de mensagens (até 32).
* `options.visibilityTimeout` -Defina um tempo de limite de invisibilidade superiores ou inferiores.

O exemplo seguinte utiliza a **getMessages** método para obter 15 mensagens numa chamada. Em seguida, processa cada mensagem, usando um for loop. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens retornados por esse método.

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
O **getQueueMetadata** devolve metadados sobre a fila, incluindo o número aproximado de mensagens em espera na fila.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Como: Lista de filas
Para obter uma lista de filas, utilize **listQueuesSegmented**. Para obter uma lista filtrada por um prefixo específico, utilize **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas não podem ser devolvidas, `result.continuationToken` pode ser utilizado como o primeiro parâmetro de **listQueuesSegmented** ou o segundo parâmetro do **listQueuesSegmentedWithPrefix** para obter mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o **deleteQueue** método no objeto de fila.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem eliminá-lo, utilize **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: Utilizar Assinaturas de Acesso Partilhado
Assinaturas de acesso partilhado (SAS) são uma forma segura para fornecer acesso granular para filas sem fornecer seu nome de conta de armazenamento ou chaves. SAS, muitas vezes, é utilizada para disponibilizar acesso limitado as filas, como permitir que uma aplicação móvel para enviar mensagens.

Um aplicativo confiável, como um serviço baseado na nuvem gera uma SAS através de **generateSharedAccessSignature** da **QueueService**e fornece-o a uma aplicação não fidedigna ou semiconfiável. Por exemplo, uma aplicação móvel. A SAS é gerada utilizando uma política, que descreve as datas de início e de fim durante as quais a SAS é válida, bem como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso partilhado que permitirá que o titular SAS adicionar mensagens à fila e 100 minutos após a hora de criação de expirar.

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

Tenha em atenção que as informações do anfitrião tem de ser fornecidas, também, conforme necessário, quando o titular SAS tenta acessar a fila.

A aplicação de cliente, em seguida, utiliza a SAS com **QueueServiceWithSAS** para execução de operações em fila. O exemplo seguinte liga-se para a fila e cria uma mensagem.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Uma vez que a SAS foi gerada com adicionar acesso, se foi efetuada uma tentativa de ler, atualizar ou eliminar mensagens, seria retornado um erro.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Também pode utilizar uma Lista de Controlo de Acesso (ACL) para definir a política de acesso para uma SAS. Isto é útil se pretender permitir que os vários clientes acesso à fila, mas fornece políticas de acesso diferentes para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas; um para 'user1' e outro para "usuário2":

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

O exemplo seguinte obtém a ACL atual **myqueue**, em seguida, adiciona as novas políticas usando **setQueueAcl**. Esta abordagem permite:

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

Assim que a ACL é definida, em seguida, pode criar uma SAS com base no ID de uma política. O exemplo seguinte cria uma nova SAS para "user2":

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Próximos Passos
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

* Visite o [blogue da equipa de armazenamento do Azure][Azure Storage Team Blog].
* Visite o [SDK de armazenamento do Azure para o nó] [ Azure Storage SDK for Node] repositório no GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Criar uma aplicação web do node. js no App Service do Azure](../../app-service/app-service-web-get-started-nodejs.md)

[Compilar e implementar uma aplicação Node.js num Serviço Cloud do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
