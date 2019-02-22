---
title: Como utilizar filas do Service Bus em node. js | Documentos da Microsoft
description: Saiba como utilizar filas do Service Bus no Azure a partir de uma aplicação node. js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 09/10/2018
ms.author: aschhab
ms.openlocfilehash: 32b566056de76d4e73b88c7ce37e148b4ecc3fd7
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587876"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Como utilizar filas do Service Bus com o node. js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar filas do Service Bus com o node. js. Os exemplos são escritos em JavaScript e utilizam o módulo do Azure node. js. Os cenários abrangidos incluem **criando filas**, **enviar e receber mensagens**, e **eliminar filas**. Para obter mais informações sobre as filas, consulte a [próximos passos](#next-steps) secção.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]


## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação node. js em branco. Para obter instruções sobre como criar uma aplicação node. js, consulte [criar e implementar uma aplicação node. js para um Web site do Azure][Create and deploy a Node.js application to an Azure Website], ou [serviço de nuvem de node. js] [ Node.js Cloud Service] com o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar o Azure Service Bus, transferir e utilizar o pacote do Azure node. js. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços REST do Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o nó Package Manager (NPM) para obter o pacote
1. Utilize o **Windows PowerShell para node. js** janela de comando para navegar para o **c:\\nó\\sbqueues\\WebRole1** pasta na qual criou o seu exemplo aplicação.
2. Tipo **npm instalar azure** na janela de comando, que deve resultar numa saída semelhante ao seguinte:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **node_modules**. Dentro dessa pasta encontrar os **azure** pacote, que contém as bibliotecas necessárias para aceder às filas do Service Bus.

### <a name="import-the-module"></a>Importe o módulo
Usando o bloco de notas ou outro editor de texto, adicione o seguinte na parte superior dos **Server. js** arquivo do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma ligação de Service bus do Azure
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter as informações necessárias para ligar ao Service Bus. Se esta variável de ambiente não está definido, tem de especificar as informações da conta, ao chamar `createServiceBusService`.

Para obter um exemplo de definir as variáveis de ambiente no [portal do Azure] [ Azure portal] para um Web site do Azure, consulte [aplicação de Web de node. js com o armazenamento] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O **ServiceBusService** objeto permite-lhe trabalhar com filas do Service Bus. O código seguinte cria um **ServiceBusService** objeto. Adicione-o junto à parte superior do **Server. js** arquivo, após a instrução para importar o módulo do Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao chamar `createQueueIfNotExists` sobre o **ServiceBusService** de objeto, da fila especificada é devolvida (se existir) ou, é criada uma nova fila com o nome especificado. O seguinte código utiliza `createQueueIfNotExists` para criar ou ligar para a fila com o nome `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O `createServiceBusService` método também oferece suporte a opções adicionais, que permitem-lhe substituir as definições de fila padrão, como o tempo de tamanho da fila ao vivo ou máximo da mensagem. O exemplo seguinte define o tamanho máximo da fila para 5 GB e um período de tempo (TTL) valor de 1 minuto de TTL:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **ServiceBusService**. Operações de filtragem pode incluir o Registro em log, tentar estabelecer novamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de fazer o processamento prévio sobre as opções de pedido, terá de chamar o método `next`, passando um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Esse retorno de chamada e após o processamento da `returnObject` (a resposta do pedido para o servidor), o retorno de chamada ou tem de invocar `next` se existir para continuar a processar outros filtros ou, simplesmente invocar `finalCallback`, que termina o serviço invocação.

Dois filtros que implementam a lógica de repetição são incluídos com o Azure SDK para node. js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O código seguinte cria um `ServiceBusService` objeto que usa o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem numa fila do Service Bus, suas chamadas de aplicação do `sendQueueMessage` método no **ServiceBusService** objeto. As mensagens enviadas para (e recebidas de) do Service Bus são filas **BrokeredMessage** objetos e têm um conjunto de propriedades padrão (tais como **etiqueta** e **TimeToLive**), um dicionário utilizado para reter propriedades personalizadas de específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem, passando uma cadeia de caracteres da mensagem. Eventuais propriedades padrão são preenchidas com valores predefinidos.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome `myqueue` usando `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre as quotas, consulte [quotas do Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas a partir de um fila com o `receiveQueueMessage` método no **ServiceBusService** objeto. Por predefinição, as mensagens são eliminadas da fila como eles são lidas; No entanto, pode ler (pré-visualização) e a mensagem de bloqueio sem eliminá-lo da fila definindo o parâmetro opcional `isPeekLock` para **true**.

O comportamento padrão de ler e eliminar a mensagem como parte da operação receive é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Uma vez que o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha de sistema.

Se o `isPeekLock` parâmetro estiver definido como **true**, a receção torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar `deleteMessage` método e fornecer a mensagem a ser eliminada como um parâmetro. O `deleteMessage` método marca a mensagem como consumida e remove-a da fila.

O exemplo seguinte demonstra como receber e processar mensagens usando `receiveQueueMessage`. O exemplo recebe pela primeira vez e elimina uma mensagem e, em seguida, recebe uma mensagem através de `isPeekLock` definido como **verdadeira**, em seguida, elimina a mensagem utilizando `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o `unlockMessage` método no **ServiceBusService** objeto. Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na fila e, se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus irá desbloquear automaticamente a mensagem e torná-lo disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a `deleteMessage` método é chamado, em seguida, a mensagem será reenviada para o aplicativo quando ele for reiniciado. Isto é frequentemente chamado *, pelo menos, uma vez processamento*, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Tal é, frequentemente, conseguido através da propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as filas, consulte os seguintes recursos.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Azure SDK para o nó] [ Azure SDK for Node] repositório no GitHub
* [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
