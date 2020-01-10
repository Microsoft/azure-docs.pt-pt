---
title: Usar filas do barramento de serviço do Azure no node. js usando o pacote Azure-SB
description: 'Início rápido: saiba como usar filas do barramento de serviço no Azure de um aplicativo node. js.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 5fa74bdc632154e361fc4d95ed602e4b4d39a198
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462179"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Início rápido: usar filas do barramento de serviço no Azure com node. js e o pacote Azure-SB

> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, você aprenderá a criar aplicativos node. js para enviar mensagens e receber mensagens de uma fila do barramento de serviço do Azure usando o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) . Os exemplos são escritos em JavaScript e usam o [módulo do Azure](https://www.npmjs.com/package/azure) node. js que usa internamente o pacote Azure-SB.

O pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) usa [APIs REST de tempo de execução do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) que usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md)mais rápido. Para saber mais sobre o novo pacote, consulte [como usar filas do barramento de serviço com o Node. js e o pacote de @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), caso contrário, continue lendo para ver como usar o pacote [do Azure](https://www.npmjs.com/package/azure) .

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando o Node. js neste tutorial. 
 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie um aplicativo node. js em branco. Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js em um site do Azure][Create and deploy a Node.js application to an Azure Website]ou [serviço de nuvem do node. js][Node.js Cloud Service] usando o Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Para usar o barramento de serviço do Azure, baixe e use o pacote do Azure do node. js. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o Gerenciador de pacotes de nó (NPM) para obter o pacote
1. Use a janela de comando do **Windows PowerShell para node. js** para navegar até o **nó c:\\\\pasta do sbqueues\\WebRole1** em que você criou o aplicativo de exemplo.
2. Digite **NPM install Azure** na janela de comando, que deve resultar em uma saída semelhante ao exemplo a seguir:

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
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **node_modules**. Dentro dessa pasta, localize o pacote **do Azure** , que contém as bibliotecas necessárias para acessar as filas do barramento de serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma conexão do barramento de serviço do Azure
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter as informações necessárias para se conectar ao barramento de serviço. Se essa variável de ambiente não estiver definida, você deverá especificar as informações da conta ao chamar `createServiceBusService`.

Para obter um exemplo de como definir as variáveis de ambiente no [portal do Azure][Azure portal] para um site do Azure, consulte [aplicativo Web do node. js com armazenamento][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusService** permite que você trabalhe com filas do barramento de serviço. O código a seguir cria um objeto **ServiceBusService** . Adicione-o próximo à parte superior do arquivo **Server. js** , após a instrução para importar o módulo do Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Chamando `createQueueIfNotExists` no objeto **ServiceBusService** , a fila especificada é retornada (se existir) ou uma nova fila com o nome especificado é criada. O código a seguir usa `createQueueIfNotExists` para criar ou conectar-se à fila chamada `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O método `createServiceBusService` também dá suporte a opções adicionais, que permitem que você substitua as configurações de fila padrão, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e um valor TTL (vida útil) de 1 minuto:

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
Operações de filtragem opcionais podem ser aplicadas às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de fazer seu pré-processamento nas opções de solicitação, o método deve chamar `next`, passando um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e depois de processar o `returnObject` (a resposta da solicitação para o servidor), o retorno de chamada deve invocar `next` se existir para continuar processando outros filtros ou invocar `finalCallback`, o que encerra a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para node. js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O código a seguir cria um objeto `ServiceBusService` que usa o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do barramento de serviço, seu aplicativo chama o método `sendQueueMessage` no objeto **ServiceBusService** . As mensagens enviadas para (e recebidas de) as filas do barramento de serviço são objetos **BrokeredMessage** e têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário usado para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando uma cadeia de caracteres como a mensagem. Todas as propriedades padrão necessárias são preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila chamada `myqueue` usando `sendQueueMessage`:

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

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens mantidas em uma fila, mas há uma limitação no tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `receiveQueueMessage` no objeto **ServiceBusService** . Por padrão, as mensagens são excluídas da fila à medida que são lidas; no entanto, você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila, definindo o parâmetro opcional `isPeekLock` como **true**.

O comportamento padrão de leitura e exclusão da mensagem como parte da operação Receive é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando ocorrer uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e depois falha antes de processá-la. Como o barramento de serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `isPeekLock` for definido como **true**, o Receive se tornará uma operação de duas etapas, o que possibilitará o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando `deleteMessage` método e fornecendo a mensagem a ser excluída como um parâmetro. O método `deleteMessage` marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir demonstra como receber e processar mensagens usando `receiveQueueMessage`. O exemplo primeiro recebe e exclui uma mensagem e, em seguida, recebe uma mensagem usando `isPeekLock` definido como **true**e, em seguida, exclui a mensagem usando `deleteMessage`:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlockMessage` no objeto **ServiceBusService** . Isso fará com que o barramento de serviço desbloqueie a mensagem dentro da fila e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `deleteMessage` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele for reiniciado. Essa abordagem é geralmente chamada *de processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica adicional ao aplicativo para lidar com a entrega de mensagens duplicadas. Geralmente, ele é obtido usando a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre filas, confira os recursos a seguir.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* Repositório do [SDK do Azure para nó][Azure SDK for Node] no github
* [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
