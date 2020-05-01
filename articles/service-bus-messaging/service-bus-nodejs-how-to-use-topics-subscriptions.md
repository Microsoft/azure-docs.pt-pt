---
title: Utilize tópicos de ônibus de serviço Azure com pacote azure/service-bus Node.js
description: Saiba como utilizar tópicos e subscrições de ônibus de serviço em Azure a partir de uma aplicação Node.js usando o pacote azure/service-bus.
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
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330451"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Como utilizar tópicos e subscrições de ônibus de serviço com Node.js e o pacote azure-sb
Neste tutorial, você aprende a criar aplicações Node.js para enviar mensagens para um tópico de ônibus de serviço e receber mensagens de uma subscrição de Ônibus de serviço usando o pacote [azure-sb.](https://www.npmjs.com/package/azure-sb) As amostras estão escritas no JavaScript e utilizam o módulo `azure-sb` Node.js [Azure](https://www.npmjs.com/package/azure) que utiliza internamente a embalagem.

> [!IMPORTANT]
> O pacote [azure-sb](https://www.npmjs.com/package/azure-sb) utiliza APIs de [tempo de execução service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). Você pode obter uma experiência [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) mais rápida usando o novo pacote que usa o [protocolo AMQP 1.0](service-bus-amqp-overview.md)mais rápido . 
> 
> Para saber mais sobre o novo pacote, consulte [como utilizar tópicos e @azure/service-bus subscrições de Ônibus de serviço com node.js e pacote](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), caso contrário continue a ler para ver como usar o pacote [azure.](https://www.npmjs.com/package/azure)

Os cenários aqui abordados incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Apagar tópicos e subscrições 

Para mais informações sobre tópicos e subscrições, consulte a secção [Next steps.](#next-steps)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do seu Estúdio Visual ou dos benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um espaço de **nome** de ônibus de serviço e obter a cadeia de **ligação**.

    > [!NOTE]
    > Você vai criar um **tópico** e uma **subscrição** para o tópico usando **Node.js** neste arranque rápido. 

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação node.js em branco. Para obter instruções sobre a criação de uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js para um Web Site Azure], [Node.js Cloud Service][Node.js Cloud Service] utilizando o Windows PowerShell ou Web Site com WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar o Service Bus, faça o download do pacote Node.js Azure. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use O Gestor de Pacotes do Nó (NPM) para obter o pacote
1. Abra uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Navegue para a pasta onde criou a sua aplicação de amostra.
3. Digite **npm instale azul** na janela de comando, o que deve resultar na seguinte saída:

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
3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta de **\_módulos** de nó. Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder aos tópicos do Service Bus.

### <a name="import-the-module"></a>Importar o módulo
Utilizando o Bloco de Notas ou outro editor de texto, adicione o seguinte ao ficheiro topo do **servidor.js** da aplicação:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
O módulo Azure lê `AZURE_SERVICEBUS_CONNECTION_STRING` a variável ambiental para a cadeia de ligação que obteve do passo anterior, "Obtenha as credenciais". Se esta variável ambiental não estiver definida, `createServiceBusService`deve especificar as informações da conta ao ligar .

Para um exemplo de definição das variáveis ambientais para um Serviço azure cloud, consulte [as variáveis ambiente definidas](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Criar um tópico
O objeto **ServiceBusService** permite-lhe trabalhar com tópicos. O seguinte código cria um objeto **ServiceBusService.** Adicione-o perto da parte superior do ficheiro **server.js,** após a declaração para importar o módulo azul:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Se ligar `createTopicIfNotExists` para o objeto **ServiceBusService,** o tópico especificado é devolvido (se existir), ou é criado um novo tópico com o nome especificado. O seguinte `createTopicIfNotExists` código utiliza para criar `MyTopic`ou ligar ao tópico denominado:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

O `createTopicIfNotExists` método também suporta opções adicionais, que lhe permitem anular as definições de tópico padrão, tais como tempo de mensagem para viver ou tamanho máximo do tópico. 

O exemplo que se segue define o tamanho máximo do tópico para 5 GB com um tempo para viver de um minuto:

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
As operações de filtragem opcionais podem ser aplicadas às operações realizadas através do **ServiceBusService**. As operações de filtragem podem incluir a exploração madeireira, a retentar automaticamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após a realização do pré-processamento `next`das opções de pedido, o método chama, e passa uma chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após `returnObject` o processamento da (resposta do pedido ao servidor), o backback deve invocar o próximo `finalCallback` (se existir) para continuar a processar outros filtros, ou invocar para acabar com a invocação do serviço.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **ServiceBusService** que utiliza o **ExponencialRetryPolicyFilter:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Criar subscrições
As subscrições de tópicos também são criadas com o objeto **ServiceBusService.** As subscrições são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da subscrição.

> [!NOTE]
> Por predefinição, as subscrições são persistentes até que eles, ou o tópico a que estão associados, sejam eliminados. Se a sua aplicação contiver lógica para criar uma subscrição, deve primeiro verificar se a subscrição existe utilizando o `getSubscription` método.
>
> Pode ter as subscrições automaticamente eliminadas, definindo a [propriedade AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O filtro **MatchAll** é o filtro predefinido utilizado quando uma subscrição é criada. Quando utiliza o filtro **MatchAll**, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição chamada AllMessages e utiliza o filtro **MatchAll** predefinido.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode criar filtros que lhe permitam examinar quais as mensagens enviadas para um tópico que devem aparecer dentro de uma subscrição específica de tópicos.

O tipo de filtro mais flexível suportado por subscrições é o **SqlFilter,** que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, reveja a sintaxe [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Os filtros podem ser adicionados `createRule` a uma subscrição utilizando o método do objeto **ServiceBusService.** Este método permite-lhe adicionar novos filtros a uma subscrição existente.

> [!NOTE]
> Uma vez que o filtro predefinido é aplicado automaticamente a todas as novas subscrições, tem primeiro de remover o filtro predefinido ou o **MatchAll** sobrepor-se-á a quaisquer outros filtros que possa especificar. Pode remover a regra predefinida utilizando o `deleteRule` método do objeto **ServiceBusService.**
>
>

O exemplo seguinte cria `HighMessages` uma subscrição com um **SqlFilter** `messagenumber` que seleciona apenas mensagens que tenham uma propriedade personalizada superior a 3:

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

Da mesma forma, o exemplo `LowMessages` seguinte cria uma subscrição com um `messagenumber` **SqlFilter** que seleciona apenas mensagens que tenham uma propriedade inferior ou igual a 3:

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

Quando uma mensagem é `MyTopic`agora enviada, é entregue aos `AllMessages` recetores subscritos pela subscrição do tópico, e entregue seletivamente aos recetores subscritos pelas subscrições `HighMessages` e `LowMessages` tópicos (dependendo do conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de `sendTopicMessage` Ônibus de serviço, a sua aplicação deve usar o método do objeto **ServiceBusService.**
As mensagens enviadas para os tópicos do Service Bus são objetos **BrokeredMessage.**
Os objetos **BrokeredMessage** têm um `Label` conjunto `TimeToLive`de propriedades padrão (como e), um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de cordas. Uma aplicação pode definir o corpo da mensagem `sendTopicMessage` passando um valor de cadeia para as propriedades padrão necessárias são povoadas por valores padrão.

O exemplo que se segue demonstra `MyTopic`como enviar cinco mensagens de teste para . O `messagenumber` valor patrimonial de cada mensagem varia na iteração do loop (esta propriedade determina quais as subscrições que a recebem):

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens realizadas num tópico, mas há um limite para o tamanho total das mensagens realizadas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas de uma subscrição utilizando o `receiveSubscriptionMessage` método no objeto **ServiceBusService.** Por predefinição, as mensagens são eliminadas da subscrição à medida que são lidas. No entanto, pode definir `isPeekLock` o parâmetro opcional para **ler** (espreitar) e bloquear a mensagem sem a apagar da subscrição.

O comportamento padrão de ler e apagar a mensagem como parte da operação de receção é o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando há uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emita o pedido de receção e, em seguida, se despenhe antes de processá-lo. Como o Service Bus marcou a mensagem como sendo consumida, então quando a aplicação recomeça e começa a consumir mensagens novamente, perdeu a mensagem que foi consumida antes do acidente.

Se `isPeekLock` o parâmetro estiver definido como **verdadeiro,** o recebimento torna-se uma operação em duas fases, o que permite suportar aplicações que não podem tolerar mensagens perdidas. Quando a Service Bus recebe um pedido, encontra a próxima mensagem a consumir, bloqueia-a para evitar que outros consumidores o recebam e devolve-a à aplicação.
Após o processo da aplicação a mensagem (ou armazená-la de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, chamando o método **deleteMessage,** e passa a mensagem para apagar como parâmetro. O método **deleteMessage** marca a mensagem como consumida e remove-a da subscrição.

O exemplo que se segue demonstra como `receiveSubscriptionMessage`as mensagens podem ser recebidas e processadas utilizando . O exemplo recebe e elimina primeiro uma mensagem da subscrição 'LowMessages', e depois recebe `isPeekLock` uma mensagem da subscrição 'HighMessages' utilizando o conjunto verdadeiro. Em seguida, elimina `deleteMessage`a mensagem utilizando:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem `unlockMessage` por alguma razão, então pode ligar para o método no objeto **ServiceBusService.** Este método faz com que o Service Bus desbloqueie a mensagem dentro da subscrição e a disponibilize novamente para ser recebida. Neste caso, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da subscrição. Se a aplicação não processar a mensagem antes de expirar o prazo de bloqueio (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser recebida novamente.

No caso de a aplicação falhar após `deleteMessage` o processamento da mensagem, mas antes de o método ser chamado, a mensagem é reentregue à aplicação quando reinicia. Este comportamento é frequentemente chamado *de Pelo menos uma vez processamento*. Ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não puder tolerar o processamento duplicado, então deve adicionar lógica à sua aplicação para lidar com a entrega de mensagens duplicadas. Pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e subscrições são persistentes a menos que a [propriedade autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) seja definida, e deve ser explicitamente eliminada através do [portal Azure][Azure portal] ou programáticamente.
O exemplo que se segue demonstra `MyTopic`como eliminar o tópico denominado:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O exemplo que se segue `HighMessages` mostra `MyTopic` como eliminar uma subscrição nomeada a partir do tópico:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais.

* Ver [Filas, tópicos e subscrições.][Queues, topics, and subscriptions]
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

