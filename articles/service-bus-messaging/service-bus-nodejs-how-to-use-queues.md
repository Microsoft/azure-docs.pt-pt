---
title: Use filas de ônibus de serviço Azure em Node.js usando pacote azure-sb
description: Saiba como criar aplicações Node.js para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service usando o pacote azure-sb.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 0e94d21bd6550a7d62ef3e7dec302c53f9851a4a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326325"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Use as filas de ônibus de serviço em Azure com Node.js e o pacote azure-sb
Neste tutorial, aprende-se a criar aplicações Node.js para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service usando o pacote [azure-sb.](https://www.npmjs.com/package/azure-sb) As amostras são escritas em JavaScript e utilizam o [módulo Azure](https://www.npmjs.com/package/azure) Node.js que utiliza internamente o pacote azure-sb.

> [!IMPORTANT]
> O pacote [azure-sb](https://www.npmjs.com/package/azure-sb) utiliza [APIs de tempo de funcionamento do Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) que usa o protocolo [AMQP 1.0](service-bus-amqp-overview.md)mais rápido . 
> 
> Para saber mais sobre o novo pacote, consulte [Como usar as filas de Service Bus com Node.js e @azure/service-bus pacote,](./service-bus-nodejs-how-to-use-queues-new-package.md)caso contrário continue a ler para ver como usar o pacote [azul.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila.
    1. Leia a **visão geral** rápida das filas de **autocarros**de serviço. 
    2. Crie um espaço de nomes de **autocarro de serviço.** 
    3. Obtenha a **cadeia de ligação.** 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nomes do Service Bus usando Node.js neste tutorial. 
 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação de Node.js em branco. Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Website Azure][Create and deploy a Node.js application to an Azure Website], ouNode.js Cloud [ Service][Node.js Cloud Service] utilizando o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar o Azure Service Bus, baixe e use o pacote Node.js Azure. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gestor de Pacotes de Nó (NPM) para obter o pacote
1. Utilize o **Windows PowerShell para Node.js** janela de comando para navegar para a pasta ** \\ c: nó \\ sbqueues \\ WebRole1** em que criou a sua aplicação de amostra.
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
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **node_modules**. Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder às filas do Service Bus.

### <a name="import-the-module"></a>Importar o módulo
Utilizando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao topo do ficheiro **server.js** da aplicação:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Criar uma ligação de autocarro azure service
O módulo Azure lê a variável ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter informações necessárias para ligar ao Service Bus. Se esta variável ambiental não estiver definida, deve especificar as informações da conta ao ligar `createServiceBusService` .

Para um exemplo de definição das variáveis ambientais no [portal Azure][Azure portal] para um Website Azure, consulte [Node.js Aplicação Web com Armazenamento.][Node.js Web Application with Storage]

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusService** permite-lhe trabalhar com as filas do Service Bus. O seguinte código cria um objeto **ServiceBusService.** Adicione-o perto do topo do ficheiro **server.js, ** após a declaração de importação do módulo Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao chamar `createQueueIfNotExists` o objeto **ServiceBusService,** a fila especificada é devolvida (se existir), ou é criada uma nova fila com o nome especificado. O seguinte código utiliza `createQueueIfNotExists` para criar ou ligar à fila denominada: `myqueue`

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O `createServiceBusService` método também suporta opções adicionais, que permitem anular as definições de fila predefinido, tais como o tempo de mensagem para viver ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila para 5 GB, e um tempo para viver (TTL) valor de 1 minuto:

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
As operações de filtragem opcionais podem ser aplicadas às operações realizadas através do **ServiceBusService**. As operações de filtragem podem incluir registos, retagem automaticamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de ter feito o pré-processamento nas opções de pedido, o método deve `next` ligar, passando uma chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após o processamento `returnObject` da (resposta do pedido ao servidor), o retorno deve invocar `next` se existir para continuar a processar outros filtros, ou invocar `finalCallback` , o que termina a invocação do serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter` . O seguinte código cria um `ServiceBusService` objeto que utiliza o `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila de ônibus de serviço, a sua aplicação liga para o `sendQueueMessage` método no objeto **ServiceBusService.** As mensagens enviadas para (e recebidas de) as filas de autocarros de serviço são **objetos de IntermedMessage,** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive),** um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrária. Uma aplicação pode definir o corpo da mensagem passando uma corda como a mensagem. Quaisquer propriedades padrão necessárias são povoadas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila `myqueue` `sendQueueMessage` denominada:

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

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens guardadas numa fila, mas há um limite para o tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila utilizando o `receiveQueueMessage` método no objeto **ServiceBusService.** Por predefinição, as mensagens são apagadas da fila à medida que são lidas; no entanto, pode ler (espreitar) e bloquear a mensagem sem a apagar da fila, definindo o parâmetro opcional `isPeekLock` para **verdadeiro**.

O comportamento padrão de ler e apagar a mensagem como parte da operação de receção é o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emite o pedido de receber e, em seguida, cai antes de o processar. Como o Service Bus terá marcado a mensagem como sendo consumida, então quando a aplicação recomeçar e começar a consumir mensagens novamente, terá perdido a mensagem que foi consumida antes do acidente.

Se o `isPeekLock` parâmetro for definido como **verdadeiro,** o receber torna-se uma operação de duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após o fim da aplicação no processamento da mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, através do método de chamada `deleteMessage` e fornecendo a mensagem para ser eliminada como parâmetro. O `deleteMessage` método marca a mensagem como sendo consumida e remove-a da fila.

O exemplo a seguir demonstra como receber e processar mensagens utilizando `receiveQueueMessage` . O exemplo primeiro recebe e elimina uma mensagem e, em seguida, recebe uma mensagem usando `isPeekLock` definido para **verdadeiro**, em seguida, elimina a mensagem usando `deleteMessage` :

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não for capaz de processar a mensagem por algum motivo, então pode ligar para o `unlockMessage` método no objeto **ServiceBusService.** fará com que a Service Bus desbloqueie a mensagem dentro da fila e a disponibilize para ser recebida novamente, seja pela mesma aplicação consumista ou por outra aplicação consumista.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `deleteMessage` método ser chamado, a mensagem será reenrimida à aplicação quando reiniciar. Esta abordagem é muitas vezes chamada *pelo menos uma vez no processamento*, isto é, cada mensagem será processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenvida. Se o cenário não pode tolerar o processamento duplicado, então os desenvolvedores de aplicações devem adicionar lógica adicional à sua aplicação para lidar com a entrega de mensagens duplicadas. É frequentemente alcançado usando a propriedade **MessageId** da mensagem, que permanecerá constante em todas as tentativas de entrega.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as filas, consulte os seguintes recursos.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Azure SDK para][Azure SDK for Node] repositório de nó no GitHub
* [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/quickstart-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md