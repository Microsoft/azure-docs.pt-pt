---
title: Use tópicos de ônibus de serviço Azure com pacote de Node.js azure/service-bus
description: Saiba como utilizar tópicos e subscrições do Service Bus em Azure a partir de uma aplicação de Node.js utilizando o pacote azure/service-bus.
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
ms.openlocfilehash: 91fae982f53af8da359baaff685996c0d1cb57c2
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976562"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Como usar tópicos e subscrições de Service Bus com Node.js e o pacote azure-sb
Neste tutorial, aprende a criar aplicações Node.js para enviar mensagens para um tópico de Service Bus e receber mensagens de uma assinatura service bus usando o pacote [azure-sb.](https://www.npmjs.com/package/azure-sb) As amostras são escritas em JavaScript e utilizam o [módulo Azure](https://www.npmjs.com/package/azure) Node.js que utiliza internamente a `azure-sb` embalagem.

> [!IMPORTANT]
> O pacote [azure-sb](https://www.npmjs.com/package/azure-sb) utiliza [APIs de tempo de funcionamento do Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência mais rápida usando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote que usa o protocolo [AMQP 1.0](service-bus-amqp-overview.md)mais rápido . 
> 
> Para saber mais sobre o novo pacote, consulte [Como usar tópicos e subscrições do Service Bus com Node.js e @azure/service-bus pacote](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), caso contrário continue a ler para ver como usar o pacote [azul.](https://www.npmjs.com/package/azure)

Os cenários aqui abordados incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Excluir tópicos e subscrições 

Para obter mais informações sobre tópicos e subscrições, consulte a secção [Etapas Seguintes.](#next-steps)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu subscritor Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **espaço de nomes** de Service Bus e obter a **cadeia de ligação**.

    > [!NOTE]
    > Irá criar um **tópico** e uma **subscrição** do tema utilizando **Node.js** neste arranque rápido. 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação de Node.js em branco. Para obter instruções sobre a criação de uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Web Site Azure],Node.js Cloud [Service][Node.js Cloud Service] utilizando o Windows PowerShell ou Web Site com WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar o Service Bus, descarregue o pacote Node.js Azure. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gestor de Pacotes de Nó (NPM) para obter o pacote
1. Abra uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Navegue para a pasta onde criou a sua aplicação de amostra.
3. Tipo **npm instalar azul** na janela de comando, o que deve resultar na seguinte saída:

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
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **de módulos de \_ nó.** Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder aos tópicos do Service Bus.

### <a name="import-the-module"></a>Importar o módulo
Utilizando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao topo do ficheiro **server.js** da aplicação:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
O módulo Azure lê a variável ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para a cadeia de ligação que obteve como parte dos [pré-requisitos](#prerequisites). Se precisar de instruções para voltar a obter a cadeia de ligação, consulte [obter a cadeia de ligação](service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string). Se esta variável ambiental não estiver definida, deve especificar as informações da conta ao ligar `createServiceBusService` .

Para um exemplo de definição das variáveis ambientais para um Serviço de Nuvem Azure, consulte [variáveis de ambiente definidos](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Criar um tópico
O objeto **ServiceBusService** permite-lhe trabalhar com tópicos. O seguinte código cria um objeto **ServiceBusService.** Adicione-o perto do topo do ficheiro **server.js,** após a declaração de importação do módulo azul:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Se ligar para `createTopicIfNotExists` o objeto **ServiceBusService,** o tópico especificado é devolvido (se existir), ou é criado um novo tópico com o nome especificado. O seguinte código utiliza `createTopicIfNotExists` para criar ou ligar ao tópico chamado `MyTopic` :

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

O `createTopicIfNotExists` método também suporta opções adicionais, que permitem anular as definições de tópicos predefinidos, tais como o tempo de mensagem para viver ou o tamanho máximo do tópico. 

O exemplo a seguir define o tamanho máximo do tópico para 5 GB com um tempo para viver de um minuto:

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
As operações de filtragem opcionais podem ser aplicadas às operações realizadas através do **ServiceBusService**. As operações de filtragem podem incluir registos, retagem automaticamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após a realização do pré-processamento nas opções de pedido, o método `next` chama, e transmite uma chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após o processamento `returnObject` da (resposta do pedido ao servidor), a chamada deve invocar o próximo (se existir) para continuar a processar outros filtros, ou invocar `finalCallback` para acabar com a invocação de serviço.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que utiliza o **ExponenciaialRetryPolicyFilter:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Criar subscrições
As subscrições de tópicos também são criadas com o objeto **ServiceBusService.** As subscrições são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da subscrição.

> [!NOTE]
> Por padrão, as subscrições são persistentes até que as subscrições sejam eliminadas. Se a sua aplicação contiver lógica para criar uma subscrição, deve primeiro verificar se a subscrição existe utilizando o `getSubscription` método.
>
> Pode eliminar automaticamente as subscrições definindo a [propriedade AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O filtro **MatchAll** é o filtro predefinido utilizado quando uma subscrição é criada. Quando utiliza o filtro **MatchAll**, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo a seguir cria uma subscrição chamada AllMessages e utiliza o filtro **MatchAll** predefinido.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode criar filtros que lhe permitam estender o âmbito das mensagens enviadas a um tópico que devem aparecer dentro de uma subscrição de tópicos específicos.

O tipo de filtro mais flexível suportado por subscrições é o **SqlFilter,** que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, reveja a sintaxe [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Os filtros podem ser adicionados a uma subscrição utilizando o `createRule` método do objeto **ServiceBusService.** Este método permite-lhe adicionar novos filtros a uma subscrição existente.

> [!NOTE]
> Como o filtro predefinido é aplicado automaticamente a todas as novas subscrições, tem primeiro de remover o filtro predefinido ou o **MatchAll** irá substituir quaisquer outros filtros que possa especificar. Pode remover a regra por defeito utilizando o `deleteRule` método do objeto **ServiceBusService.**
>
>

O exemplo a seguir cria uma subscrição nomeada `HighMessages` com um **SqlFilter** que apenas seleciona mensagens que têm uma propriedade personalizada `messagenumber` superior a 3:

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

Da mesma forma, o exemplo a seguir cria uma subscrição nomeada `LowMessages` com um **SqlFilter** que apenas seleciona mensagens que têm uma `messagenumber` propriedade inferior ou igual a 3:

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

Quando uma mensagem é agora enviada para `MyTopic` , é entregue aos recetores subscritos à subscrição de `AllMessages` tópicos, e entregue seletivamente aos destinatários subscritos às `HighMessages` subscrições de tópicos e `LowMessages` tópicos (dependendo do conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de Service Bus, a sua aplicação deve utilizar o `sendTopicMessage` método do objeto **ServiceBusService.**
As mensagens enviadas para os tópicos do Service Bus são **objetos da BrokeredMessage.**
**Os objetos de intermediação** têm um conjunto de propriedades padrão (tais como `Label` `TimeToLive` e), um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de cordas. Uma aplicação pode definir o corpo da mensagem passando um valor de cadeia para as `sendTopicMessage` propriedades padrão e quaisquer propriedades padrão exigidas são povoadas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `MyTopic` . O `messagenumber` valor patrimonial de cada mensagem varia na iteração do loop (esta propriedade determina quais as subscrições que a recebem):

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens mantidas num tópico, mas há um limite para o tamanho total das mensagens detidas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas a partir de uma subscrição utilizando o `receiveSubscriptionMessage` método no objeto **ServiceBusService.** Por predefinição, as mensagens são eliminadas da subscrição à medida que são lidas. No entanto, pode definir o parâmetro opcional `isPeekLock` para **ser verdadeiro** para ler (espreitar) e bloquear a mensagem sem a eliminar da subscrição.

O comportamento padrão de ler e apagar a mensagem como parte da operação de receção é o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando há uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emite o pedido de receber e, em seguida, cai antes de o processar. Como a Service Bus marcou a mensagem como sendo consumida, então quando a aplicação reinicia e volta a consumir mensagens, perdeu a mensagem que foi consumida antes do acidente.

Se o `isPeekLock` parâmetro for definido como **verdadeiro,** o receber torna-se uma operação em duas fases, o que permite suportar aplicações que não podem tolerar mensagens perdidas. Quando a Service Bus recebe um pedido, encontra a próxima mensagem a consumir, bloqueia-a para evitar que outros consumidores o recebam e a devolve à aplicação.
Após o processo de aplicação a mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção chamando o método **de eliminação de mensagens** e passa a mensagem para apagar como parâmetro. O método **deleteMessage** marca a mensagem como consumida e remove-a da subscrição.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas utilizando `receiveSubscriptionMessage` . O exemplo primeiro recebe e elimina uma mensagem da subscrição 'LowMessages' e, em seguida, recebe uma mensagem da subscrição 'HighMessages' usando `isPeekLock` definido para verdadeiro. Em seguida, elimina a mensagem `deleteMessage` utilizando:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não for capaz de processar a mensagem por algum motivo, então pode ligar para o `unlockMessage` método no objeto **ServiceBusService.** Este método faz com que o Service Bus desbloqueie a mensagem dentro da subscrição e a disponibilize para ser novamente recebida. Neste caso, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um tempo limite associado a uma mensagem bloqueada dentro da subscrição. Se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `deleteMessage` método ser chamado, a mensagem é reentrexada na aplicação quando reinicia. Este comportamento é frequentemente chamado *pelo menos uma vez processando*. Ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o cenário não pode tolerar o processamento duplicado, então deve adicionar lógica à sua aplicação para lidar com a entrega de mensagens duplicadas. Pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e subscrições são persistentes a menos que a [propriedade autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) esteja definida, e deve ser explicitamente eliminada através do [portal Azure][Azure portal] ou programáticamente.
O exemplo a seguir demonstra como eliminar o tópico denominado `MyTopic` :

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O exemplo a seguir mostra como eliminar uma subscrição nomeada `HighMessages` do `MyTopic` tópico:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais.

* Consulte [filas, tópicos e subscrições.][Queues, topics, and subscriptions]
* Referência da API para [SqlFilter][SqlFilter].
* Visite o [Azure SDK para][Azure SDK for Node] repositório de nó no GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Criar e implementar uma aplicação Node.js para um Web Site Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

