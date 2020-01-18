---
title: Use os tópicos do barramento de serviço do Azure com o pacote de Node. js do Azure/Service-Bus
description: Saiba como usar os tópicos e as assinaturas do barramento de serviço no Azure de um aplicativo node. js usando o pacote do Azure/Service-Bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: e6ac3c74a0a8346ad8a6820c676224bfcba0e8b4
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264446"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Início rápido: como usar os tópicos e as assinaturas do barramento de serviço com o Node. js e o pacote do Azure-SB
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Neste tutorial, você aprenderá a criar aplicativos node. js para enviar mensagens para um tópico do barramento de serviço e receber mensagens de uma assinatura do barramento de serviço usando o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) . Os exemplos são escritos em JavaScript e usam o [módulo do Azure](https://www.npmjs.com/package/azure) node. js que usa internamente o pacote de `azure-sb`.

O pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) usa [APIs REST de tempo de execução do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) que usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md)mais rápido. Para saber mais sobre o novo pacote, consulte [como usar os tópicos e as assinaturas do barramento de serviço com o Node. js e o pacote @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), caso contrário, continue lendo para ver como usar o pacote [do Azure](https://www.npmjs.com/package/azure) .

Os cenários abordados aqui incluem:

- Criando tópicos e assinaturas 
- Criando filtros de assinatura 
- Enviando mensagens para um tópico 
- Recebendo mensagens de uma assinatura
- Excluindo tópicos e assinaturas 

Para obter mais informações sobre tópicos e assinaturas, consulte a seção [próximas etapas](#next-steps) .

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [início rápido: Use o portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **namespace** do barramento de serviço e obter a **cadeia de conexão**.

    > [!NOTE]
    > Você criará um **tópico** e uma **assinatura** para o tópico usando o **node. js** neste guia de início rápido. 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie um aplicativo node. js em branco. Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js em um site do Azure], [serviço de nuvem do node. js][Node.js Cloud Service] usando o Windows PowerShell ou site com o WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Para usar o barramento de serviço, baixe o pacote do Azure do node. js. Este pacote inclui um conjunto de bibliotecas que se comunicam com os serviços REST do barramento de serviço.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o Gerenciador de pacotes de nó (NPM) para obter o pacote
1. Abra uma interface de linha de comando, como **PowerShell** (Windows), **terminal** (Mac) ou **bash** (Unix).
2. Navegue até a pasta em que você criou o aplicativo de exemplo.
3. Digite **NPM install Azure** na janela de comando, que deve resultar na seguinte saída:

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
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node\_modules** foi criada. Dentro dessa pasta, encontre o pacote **do Azure** , que contém as bibliotecas necessárias para acessar os tópicos do barramento de serviço.

### <a name="import-the-module"></a>Importar o módulo
Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do barramento de serviço
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para a cadeia de conexão que você obteve na etapa anterior, "obter as credenciais". Se essa variável de ambiente não estiver definida, você deverá especificar as informações da conta ao chamar `createServiceBusService`.

Para obter um exemplo de como definir as variáveis de ambiente para um serviço de nuvem do Azure, consulte [definir variáveis de ambiente](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Criar um tópico
O objeto **ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **ServiceBusService** . Adicione-o próximo à parte superior do arquivo **Server. js** , após a instrução para importar o módulo do Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Se você chamar `createTopicIfNotExists` no objeto **ServiceBusService** , o tópico especificado será retornado (se existir) ou um novo tópico com o nome especificado será criado. O código a seguir usa `createTopicIfNotExists` para criar ou se conectar ao tópico chamado `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

O método `createTopicIfNotExists` também dá suporte a opções adicionais, que permitem que você substitua as configurações de tópico padrão, como a vida útil da mensagem ou o tamanho máximo do tópico. 

O exemplo a seguir define o tamanho máximo do tópico como 5 GB com uma vida útil de um minuto:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas às operações executadas usando **ServiceBusService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de executar o pré-processamento nas opções de solicitação, o método chama `next`e passa um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e depois de processar o `returnObject` (a resposta da solicitação para o servidor), o retorno de chamada deve invocar Next (se existir) para continuar processando outros filtros ou invocar `finalCallback` para encerrar a invocação de serviço.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O código a seguir cria um objeto **ServiceBusService** que usa o **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Criar assinaturas
As assinaturas de tópico também são criadas com o objeto **ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [!NOTE]
> Por padrão, as assinaturas são persistentes até que elas ou o tópico ao qual estão associadas sejam excluídas. Se seu aplicativo contiver lógica para criar uma assinatura, ele deverá primeiro verificar se a assinatura existe usando o método `getSubscription`.
>
> Você pode fazer com que as assinaturas sejam excluídas automaticamente definindo a [Propriedade AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O filtro **filtro matchall** é o filtro padrão usado quando uma assinatura é criada. Quando utiliza o filtro **MatchAll**, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo a seguir cria uma assinatura chamada mymessages e usa o filtro **filtro matchall** padrão.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Você também pode criar filtros que permitem o escopo de quais mensagens enviadas a um tópico devem aparecer em uma assinatura de tópico específica.

O tipo de filtro mais flexível com suporte das assinaturas é o **sqlfilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [sqlfilter. Sqlexpression][SqlFilter.SqlExpression] .

Os filtros podem ser adicionados a uma assinatura usando o método `createRule` do objeto **ServiceBusService** . Esse método permite que você adicione novos filtros a uma assinatura existente.

> [!NOTE]
> Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, primeiro você deve remover o filtro padrão ou o **filtro matchall** substituirá quaisquer outros filtros que você possa especificar. Você pode remover a regra padrão usando o método `deleteRule` do objeto **ServiceBusService** .
>
>

O exemplo a seguir cria uma assinatura chamada `HighMessages` com um **sqlfilter** que seleciona apenas as mensagens que têm uma propriedade personalizada `messagenumber` maior que 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Da mesma forma, o exemplo a seguir cria uma assinatura chamada `LowMessages` com um **sqlfilter** que seleciona apenas as mensagens que têm uma propriedade `messagenumber` menor ou igual a 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando uma mensagem é enviada para `MyTopic`, ela é entregue aos destinatários inscritos na assinatura do tópico `AllMessages` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do tópico `HighMessages` e `LowMessages` (dependendo do conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do barramento de serviço, seu aplicativo deve usar o método `sendTopicMessage` do objeto **ServiceBusService** .
As mensagens enviadas aos tópicos do barramento de serviço são objetos **BrokeredMessage** .
Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como `Label` e `TimeToLive`), um dicionário que é usado para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados de cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem passando um valor de cadeia de caracteres para a `sendTopicMessage` e todas as propriedades padrão necessárias são preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `MyTopic`. O valor da propriedade `messagenumber` de cada mensagem varia de acordo com a iteração do loop (essa propriedade determina quais assinaturas o receberão):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens mantidas em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
As mensagens são recebidas de uma assinatura usando o método `receiveSubscriptionMessage` no objeto **ServiceBusService** . Por padrão, as mensagens são excluídas da assinatura à medida que são lidas. No entanto, você pode definir o parâmetro opcional `isPeekLock` como **true** para leitura (PEEK) e bloquear a mensagem sem excluí-la da assinatura.

O comportamento padrão de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando há uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e depois falha antes de processá-la. Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem que foi consumida antes da falha.

Se o parâmetro `isPeekLock` for definido como **true**, o Receive se tornará uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens perdidas. Quando o barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-a para impedir que outros consumidores a recebam e a retorna ao aplicativo.
Depois que o aplicativo processa a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método **deleteMessage** e passa a mensagem para exclusão como um parâmetro. O método **deleteMessage** marca a mensagem como consumida e a remove da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando `receiveSubscriptionMessage`. O exemplo primeiro recebe e exclui uma mensagem da assinatura ' LowMessages ' e recebe uma mensagem da assinatura ' HighMessages ' usando `isPeekLock` definido como true. Em seguida, ele exclui a mensagem usando `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlockMessage` no objeto **ServiceBusService** . Esse método faz com que o barramento de serviço desbloqueie a mensagem na assinatura e torne-a disponível para ser recebida novamente. Nessa instância, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura. Se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `deleteMessage` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse comportamento é geralmente chamado *de processamento de pelo menos uma vez*. Ou seja, cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, você deverá adicionar lógica ao seu aplicativo para lidar com a entrega de mensagens duplicada. Você pode usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e as assinaturas são persistentes, a menos que a [Propriedade autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) seja definida e deve ser explicitamente excluída por meio do [portal do Azure][Azure portal] ou programaticamente.
O exemplo a seguir demonstra como excluir o tópico chamado `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O exemplo a seguir mostra como excluir uma assinatura denominada `HighMessages` do tópico `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos dos tópicos do barramento de serviço, siga estes links para saber mais.

* Consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions].
* Referência da API para [SqlFilter][SqlFilter].
* Visite o repositório [SDK do Azure para nó][Azure SDK for Node] no github.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Criar e implantar um aplicativo node. js em um site do Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

