---
title: Como utilizar o Azure Service Bus tópicos e subscrições com node. js | Documentos da Microsoft
description: Saiba como utilizar subscrições e tópicos do Service Bus no Azure a partir de uma aplicação node. js.
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
ms.topic: article
ms.date: 10/16/2018
ms.author: aschhab
ms.openlocfilehash: c2a6acc1ce48a8671af20df6daa89498a81fdb4c
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587162"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Como tópicos do barramento de serviço de utilização e as subscrições com node. js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como utilizar tópicos do Service Bus e as subscrições de aplicações node. js. Os cenários abrangidos incluem:

- Criação de tópicos e subscrições 
- Criar filtros de subscrição 
- Enviar mensagens para um tópico 
- Receber mensagens de uma subscrição
- A eliminar tópicos e subscrições 

Para obter mais informações sobre tópicos e subscrições, veja [próximos passos](#next-steps) secção.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação node. js em branco. Para obter instruções sobre como criar uma aplicação node. js, consulte [criar e implementar uma aplicação node. js para um Web Site do Azure], [serviço Cloud de node. js] [ Node.js Cloud Service] usando o Windows PowerShell, ou Web Site com o WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar o Service Bus, transfira o pacote do Azure node. js. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços REST do Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o nó Package Manager (NPM) para obter o pacote
1. Abra uma interface de linha de comandos como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Navegue para a pasta onde criou o exemplo de aplicação.
3. Tipo **npm instalar azure** na janela de comando, que deve resultar na seguinte saída:

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
3. Pode executar manualmente a **ls** comando para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localize a **azure** pacote, que contém as bibliotecas necessárias para aceder a tópicos do Service Bus.

### <a name="import-the-module"></a>Importe o módulo
Usando o bloco de notas ou outro editor de texto, adicione o seguinte na parte superior dos **Server. js** arquivo do aplicativo:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação do Service Bus
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para a cadeia de ligação que obteve no passo anterior, "obter as credenciais". Se esta variável de ambiente não está definido, tem de especificar as informações da conta, ao chamar `createServiceBusService`.

Para obter um exemplo de definir as variáveis de ambiente para um serviço Cloud do Azure, veja [definir variáveis de ambiente](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Criar um tópico
O **ServiceBusService** objeto permite-lhe trabalhar com tópicos. O código seguinte cria um **ServiceBusService** objeto. Adicione-o junto à parte superior do **Server. js** arquivo, após a instrução para importar o módulo do azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Se chamar `createTopicIfNotExists` sobre o **ServiceBusService** de objeto, o tópico especificado é devolvido (se existir) ou, é criado um novo tópico com o nome especificado. O seguinte código utiliza `createTopicIfNotExists` para criar ou ligar para o tópico com o nome `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

O `createTopicIfNotExists` método também oferece suporte a opções adicionais, que permitem-lhe substituir as predefinições de tópico, como o tempo de live da mensagem ou tamanho de tópico máximo. 

O exemplo seguinte define o tamanho de tópico máximo para 5 GB, com um TTL de um minuto:

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
Operações de filtragem opcionais podem ser aplicadas a operações executadas usando **ServiceBusService**. Operações de filtragem pode incluir o Registro em log, tentar estabelecer novamente, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de efetuar o processamento prévio sobre as opções de pedido, chama o método `next`e passa um retorno de chamada com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Esse retorno de chamada e após o processamento da `returnObject` (a resposta do pedido para o servidor), o retorno de chamada tem o invocar em seguida (se existir) para continuar a processar outros filtros ou invocar `finalCallback` para terminar a invocação de serviço.

O Azure SDK para Node.js inclui dois filtros que implementam lógica de repetição: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O código seguinte cria um **ServiceBusService** objeto que usa o **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Criar subscrições
Subscrições de tópicos também são criadas com o **ServiceBusService** objeto. As subscrições são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da subscrição.

> [!NOTE]
> As assinaturas são persistentes até a eles ou o tópico estão associados com, são eliminados. Se a sua aplicação contém lógica para criar uma subscrição, primeiro deve dar se existe a subscrição, utilizando o `getSubscription` método.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
O **MatchAll** filtro é o filtro predefinido utilizado quando é criada uma subscrição. Quando utiliza o filtro **MatchAll**, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome AllMessages e utiliza a predefinição **MatchAll** filtro.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode criar filtros que permitem a para o âmbito em que as mensagens enviadas para um tópico deve aparecer no espaço de uma subscrição de tópico específico.

O tipo mais flexível de filtro suportado pelas subscrições é a **SqlFilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser utilizadas com um filtro SQL, reveja os [Sqlexpression] [ SqlFilter.SqlExpression] sintaxe.

Filtros podem ser adicionados a uma subscrição, utilizando o `createRule` método da **ServiceBusService** objeto. Este método permite-lhe adicionar novos filtros a uma subscrição existente.

> [!NOTE]
> Uma vez que o filtro predefinido é aplicado automaticamente a todas as novas subscrições, primeiro tem de remover o filtro predefinido ou o **MatchAll** irá substituir todos os filtros que pode especificar. Pode remover a regra predefinida, utilizando o `deleteRule` método da **ServiceBusService** objeto.
>
>

O exemplo seguinte cria uma subscrição com o nome `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens possuem `messagenumber` propriedade superior a 3:

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

Da mesma forma, o exemplo seguinte cria uma subscrição com o nome `LowMessages` com um **SqlFilter** que seleciona apenas as mensagens com um `messagenumber` propriedade menor ou igual a 3:

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

Quando uma mensagem é agora enviada `MyTopic`, que é entregue aos destinatários a `AllMessages` subscrição de tópico e entregue seletivamente aos destinatários a `HighMessages` e `LowMessages` subscrições de tópicos (consoante a conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do Service Bus, a aplicação tem de utilizar o `sendTopicMessage` método da **ServiceBusService** objeto.
As mensagens enviadas para tópicos do Service Bus são **BrokeredMessage** objetos.
**BrokeredMessage** objetos têm um conjunto de propriedades padrão (tais como `Label` e `TimeToLive`), um dicionário utilizado para reter propriedades personalizadas de específicas da aplicação e um corpo de dados de cadeia de caracteres. Uma aplicação pode definir o corpo da mensagem, passando um valor de cadeia de caracteres para o `sendTopicMessage` e todas as necessárias propriedades padrão são preenchidas por valores predefinidos.

O exemplo seguinte demonstra como enviar cinco mensagens de teste para `MyTopic`. O `messagenumber` valor da propriedade de cada mensagem varia com a iteração do loop (esta propriedade determina quais as subscrições que recebem a mesma):

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite no número de mensagens contidas num tópico, mas existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas a partir de uma subscrição com o `receiveSubscriptionMessage` método no **ServiceBusService** objeto. Por predefinição, as mensagens são eliminadas da subscrição como eles são de leitura. No entanto, pode definir o parâmetro opcional `isPeekLock` para **true** ler (pré-visualização) e a mensagem de bloqueio sem eliminá-lo a partir da subscrição.

O comportamento padrão de ler e eliminar a mensagem como parte da operação receive é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem quando houver uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-lo. Porque o Service Bus marcou a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, ele tem estado em falta a mensagem consumida antes da falha de sistema.

Se o `isPeekLock` parâmetro estiver definido como **true**, a receção torna-se uma operação de duas etapas, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte para consumir, bloqueia-a para impedir a receção por outros consumidores e devolve a mesma à aplicação.
Depois da aplicação processa a mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar **deleteMessage** método e passa a mensagem a eliminar como um parâmetro. O **deleteMessage** método marca a mensagem como consumida e remove-o da subscrição.

O exemplo seguinte demonstra como as mensagens podem ser recebidas e processados usando `receiveSubscriptionMessage`. O exemplo recebe pela primeira vez e elimina uma mensagem da subscrição "LowMessages" e, em seguida, recebe uma mensagem a partir da subscrição de 'HighMessages' com `isPeekLock` definido como true. Em seguida, elimina a mensagem utilizando `deleteMessage`:

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o `unlockMessage` método no **ServiceBusService** objeto. Esse método faz com que o Service Bus desbloqueie a mensagem dentro da subscrição e disponibilizá-lo ser recebida novamente. Neste caso, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na subscrição. Se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, no caso de falha da aplicação), em seguida, do Service Bus desbloqueia automaticamente a mensagem e torna-o disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a `deleteMessage` método é chamado, a mensagem é reenviada para a aplicação quando esta reiniciar. Este comportamento é frequentemente designado *processamento, pelo menos, uma vez*. Ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não pode tolerar o processamento duplicado, em seguida, deve adicionar lógica à sua aplicação para processar a entrega de mensagens duplicadas. Pode utilizar o **MessageId** propriedade da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Tópicos e subscrições são persistentes e tem de ser explicitamente eliminada através da [portal do Azure] [ Azure portal] ou programaticamente.
O exemplo seguinte mostra como eliminar o tópico com o nome `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O exemplo seguinte mostra como eliminar uma subscrição com o nome `HighMessages` partir o `MyTopic` tópico:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Próximos Passos
Agora que aprendeu as noções básicas de tópicos do Service Bus, siga estas ligações para saber mais.

* Ver [filas, tópicos e subscrições][Queues, topics, and subscriptions].
* Referência da API para [SqlFilter][SqlFilter].
* Visite o [SDK do Azure para o nó] [ Azure SDK for Node] repositório no GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Criar e implementar uma aplicação node. js para um Web Site do Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

