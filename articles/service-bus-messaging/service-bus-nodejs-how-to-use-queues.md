---
title: Utilize as filas de ônibus de serviço Azure em Node.js usando pacote azure-sb
description: Saiba como criar aplicações Node.js para enviar mensagens e receber mensagens de uma fila de ônibus de serviço Azure usando o pacote azure-sb.
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
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330605"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Use filas de ônibus de serviço em Azure com Node.js e o pacote azure-sb
Neste tutorial, você aprende a criar aplicações Node.js para enviar mensagens e receber mensagens de uma fila de ônibus de serviço Azure usando o pacote [azure-sb.](https://www.npmjs.com/package/azure-sb) As amostras estão escritas no JavaScript e utilizam o módulo Node.js [Azure](https://www.npmjs.com/package/azure) que utiliza internamente o pacote azure-sb.

> [!IMPORTANT]
> O pacote [azure-sb](https://www.npmjs.com/package/azure-sb) utiliza APIs de [tempo de execução service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) que usa o [protocolo AMQP 1.0](service-bus-amqp-overview.md)mais rápido . 
> 
> Para saber mais sobre o novo pacote, consulte como utilizar as filas de ônibus de [serviço com o pacote Node.js e @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), caso contrário continue a ler para ver como usar o pacote [azul.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de ônibus de serviço para criar uma fila.
    1. Leia a **visão geral** rápida das filas de **ônibus**de serviço . 
    2. Crie um espaço de **nome**de ônibus de serviço . 
    3. Pegue a corda de **ligação**. 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nome do Ônibus de serviço usando Node.js neste tutorial. 
 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação node.js em branco. Para obter instruções sobre como criar uma aplicação Node.js, consulte [Create e implemente uma aplicação Node.js para um Website Azure][Create and deploy a Node.js application to an Azure Website], ou [Node.js Cloud Service][Node.js Cloud Service] utilizando o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar o Azure Service Bus, faça o download e utilize o pacote Node.js Azure. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use O Gestor de Pacotes do Nó (NPM) para obter o pacote
1. Utilize a janela de comando **DoMPowerShell para node.js** para navegar para o **c:\\nó\\sbqueues\\pasta WebRole1** na qual criou a sua aplicação de amostra.
2. Tipo **npm instalar azul** na janela de comando, o que deve resultar numa saída semelhante ao seguinte exemplo:

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
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **node_modules**. Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder às filas de ônibus de serviço.

### <a name="import-the-module"></a>Importar o módulo
Utilizando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao ficheiro topo do **servidor.js** da aplicação:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Criar uma ligação de ônibus de serviço Azure
O módulo Azure lê a variável ambiental `AZURE_SERVICEBUS_CONNECTION_STRING` obter informações necessárias para ligar ao Bus de Serviço. Se esta variável ambiental não estiver definida, deve especificar as informações da conta ao ligar para `createServiceBusService`.

Para um exemplo de configuração das variáveis ambientais no [portal Azure][Azure portal] para um Website Azure, consulte A [Aplicação Web no Nó.js com Armazenamento][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusService** permite-lhe trabalhar com as filas de ônibus de serviço. O seguinte código cria um objeto **ServiceBusService.** Adicione-o perto do topo do ficheiro **server.js,** após a declaração de importação do módulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao ligar `createQueueIfNotExists` no objeto **ServiceBusService,** a fila especificada é devolvida (se existir), ou é criada uma nova fila com o nome especificado. O seguinte código utiliza `createQueueIfNotExists` para criar ou ligar à fila denominada `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O método `createServiceBusService` também suporta opções adicionais, que lhe permitem anular as definições de fila padrão, tais como tempo de mensagem para viver ou tamanho máximo da fila. O exemplo seguinte define o tamanho máximo da fila para 5 GB, e um tempo para viver (TTL) de 1 minuto:

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
As operações de filtragem opcionais podem ser aplicadas às operações realizadas através do **ServiceBusService**. As operações de filtragem podem incluir a exploração madeireira, a retentar automaticamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após o seu pré-processamento sobre as opções de pedido, o método deve chamar `next`, passando uma chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após o processamento do `returnObject` (a resposta do pedido ao servidor), o backback deve invocar `next` se existir para continuar a processar outros filtros, ou invocar `finalCallback`, o que termina com a invocação do serviço.

Dois filtros que implementam a lógica de retry estão incluídos com o SDK Azure para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O seguinte código cria um objeto `ServiceBusService` que utiliza o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila de ônibus de serviço, a sua aplicação chama o método `sendQueueMessage` no objeto **ServiceBusService.** As mensagens enviadas para (e recebidas de) service Bus são objetos **BrokeredMessage,** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive),** um dicionário que é usado para deter propriedades específicas de aplicação personalizadas, e um corpo de dados de aplicação arbitrárias. Uma aplicação pode definir o corpo da mensagem passando uma corda como mensagem. Quaisquer propriedades padrão necessárias são povoadas com valores padrão.

O exemplo que se segue demonstra como enviar uma mensagem de teste à fila denominada `myqueue` utilizando `sendQueueMessage`:

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

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens guardadas numa fila, mas há um limite no tamanho total das mensagens guardadas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila utilizando o método `receiveQueueMessage` no objeto **ServiceBusService.** Por predefinição, as mensagens são apagadas da fila à medida que são lidas; no entanto, pode ler (espreitar) e bloquear a mensagem sem a apagar da fila, definindo o parâmetro opcional `isPeekLock` **verdadeiro**.

O comportamento padrão de ler e apagar a mensagem como parte da operação de receção é o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emita o pedido de receção e, em seguida, se despenhe antes de processá-lo. Como o Service Bus terá marcado a mensagem como sendo consumida, então quando a aplicação recomeçar e começar a consumir mensagens novamente, terá perdido a mensagem que foi consumida antes do acidente.

Se o parâmetro `isPeekLock` estiver definido como **verdadeiro,** o recebimento torna-se uma operação de duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação terminar de processar a mensagem (ou armazená-la de forma fiável para processamento futuro), completa a segunda fase do processo de receção, chamando `deleteMessage` método e fornecendo a mensagem a ser eliminada como parâmetro. O método `deleteMessage` marca a mensagem como sendo consumida e retira-a da fila.

O exemplo que se segue demonstra como receber e processar mensagens utilizando `receiveQueueMessage`. O exemplo recebe e elimina primeiro uma mensagem e, em seguida, recebe uma mensagem usando `isPeekLock` definida **como verdadeira,** em seguida, elimina a mensagem usando `deleteMessage`:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por alguma razão, então pode ligar para o método `unlockMessage` no objeto **ServiceBusService.** fará com que a Service Bus desbloqueie a mensagem dentro da fila e a disponibilize para ser recebida novamente, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do tempo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem automaticamente e a tornará disponível para ser recebido novamente.

No caso de a aplicação se falhar após o processamento da mensagem, mas antes de ser chamada a `deleteMessage` método, a mensagem será reentregue à aplicação quando recomeçar. Esta abordagem é muitas vezes chamada *de Pelo Menos Uma Vez Processamento*, ou seja, cada mensagem será processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não tolerar o processamento duplicado, então os desenvolvedores de aplicações devem adicionar lógica adicional à sua aplicação para lidar com a entrega de mensagens duplicadas. É frequentemente conseguido usando a propriedade **MessageId** da mensagem, que permanecerá constante através de tentativas de entrega.

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre filas, consulte os seguintes recursos.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Azure SDK para][Azure SDK for Node] repositório de nó no GitHub
* [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
