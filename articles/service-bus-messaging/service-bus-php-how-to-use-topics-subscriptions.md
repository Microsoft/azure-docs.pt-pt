---
title: Como usar os tópicos do Ônibus de Serviço Azure com PHP
description: Neste tutorial, você aprende a usar tópicos e subscrições do Azure Service Bus a partir de uma aplicação PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760679"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Quickstart: Como usar tópicos e subscrições de ônibus de serviço com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra-lhe como usar tópicos e subscrições de Ônibus de serviço. As amostras são escritas em PHP e utilizam o [SDK Azure para PHP](https://github.com/Azure/azure-sdk-for-php). Os cenários abordados incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Apagar tópicos e subscrições

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do seu Estúdio Visual ou dos benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um espaço de **nome** de ônibus de serviço e obter a cadeia de **ligação**.

    > [!NOTE]
    > Irá criar um **tópico** e uma **subscrição** do tema utilizando **PHP** neste arranque rápido. 

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para a criação de uma aplicação PHP que aceda ao serviço Azure Blob é para as classes de referência no [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) a partir do seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação, ou Notepad.

> [!NOTE]
> A sua instalação PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e ativada.
> 
> 

Este artigo descreve como usar funcionalidades de serviço que podem ser chamadas dentro de uma aplicação PHP localmente, ou em código que funciona dentro de uma função web do Azure, papel do trabalhador ou website.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de clientes Azure

### <a name="install-via-composer"></a>Instalar via Compositor
1. Crie um ficheiro chamado **compositor.json** na raiz do seu projeto e adicione-lhe o seguinte código:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Baixe **[compositor.phar][compositor-phar]** na raiz do seu projeto.
3. Abra um pedido de comando e execute o seguinte comando na raiz do seu projeto
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar as APIs do autocarro de serviço:

1. Faça referência ao ficheiro auto-carregador utilizando a declaração [de require_once.][require-once]
2. Faça referência a todas as aulas que possa usar.

O exemplo que se segue mostra como incluir o ficheiro auto-loader e fazer referência à classe **ServiceBusService.**

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) assume que instalou as Bibliotecas de Clientes PHP para o Azure via Compositor. Se instalou as bibliotecas manualmente ou como pacote PEAR, deve fazer referência ao ficheiro autoloader **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos seguintes exemplos, a `require_once` declaração é sempre mostrada, mas apenas as classes necessárias para o exemplo a executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
Para instantaneamente um cliente de Ônibus de serviço, você deve primeiro ter uma cadeia de ligação válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é tipicamente `https://[yourNamespace].servicebus.windows.net`do formato .

Para criar qualquer cliente de serviço `ServicesBuilder` Azure, você deve usar a classe. Pode:

* Passe a corda de ligação diretamente para ele.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por defeito vem com suporte para uma fonte externa - variáveis ambientais.
  * Pode adicionar novas origem de dados ao expandir a classe `ConnectionStringSource`.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Você pode realizar operações de gestão para tópicos de ônibus de serviço através da `ServiceBusRestProxy` classe. Um `ServiceBusRestProxy` objeto é construído `ServicesBuilder::createServiceBusService` através do método de fábrica com uma corda de ligação adequada que encapsula as permissões simbólicas para geri-lo.

O exemplo que se segue `ServiceBusRestProxy` mostra `ServiceBusRestProxy->createTopic` como instantaneamente `mytopic` um `MySBNamespace` e chamada para criar um tópico nomeado dentro de um espaço de nome:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Pode utilizar `listTopics` o `ServiceBusRestProxy` método em objetos para verificar se já existe um tópico com um nome especificado dentro de um espaço de nome de serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
As subscrições de tópicos também são criadas com o `ServiceBusRestProxy->createSubscription` método. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro quando for criada uma nova subscrição, o filtro **MatchAll** (predefinido) é utilizado. Quando o filtro **MatchAll** é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria `mysubscription` uma subscrição nomeada e utiliza o filtro **MatchAll** predefinido.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica. O tipo de filtro mais flexível suportado por subscrições é o [SqlFilter,](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para mais informações sobre os Filtros Sql, consulte [a propriedade SqlFilter.SqlExpression][sqlfilter].

> [!NOTE]
> Cada regra sobre uma subscrição processa mensagens de entrada de forma independente, adicionando as suas mensagens de resultados à subscrição. Além disso, cada nova subscrição tem um objeto **de regra** padrão com um filtro que adiciona todas as mensagens do tópico à subscrição. Para receber apenas mensagens correspondentes ao filtro, tem de remover a regra predefinida. Pode remover a regra predefinida utilizando o `ServiceBusRestProxy->deleteRule` método.
> 
> 

O exemplo seguinte cria `HighMessages` uma subscrição com um **SqlFilter** `MessageNumber` que seleciona apenas mensagens que tenham uma propriedade personalizada superior a 3. Consulte [Enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre a adição de propriedades personalizadas às mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Este código requer a utilização de `WindowsAzure\ServiceBus\Models\SubscriptionInfo`um espaço de nome adicional: .

Da mesma forma, o exemplo `LowMessages` seguinte `SqlFilter` cria uma subscrição `MessageNumber` com um que seleciona apenas mensagens que tenham uma propriedade inferior ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem `mytopic` é enviada para o tópico, é sempre `mysubscription` entregue aos recetores subscritos à `HighMessages` subscrição, e entregue seletivamente aos recetores subscritos às e `LowMessages` subscrições (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico `ServiceBusRestProxy->sendTopicMessage` de Ônibus de serviço, a sua aplicação chama o método. O código seguinte mostra como enviar `mytopic` uma mensagem para `MySBNamespace` o tópico anteriormente criado dentro do espaço de nome do serviço.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

As mensagens enviadas para os tópicos do Service Bus são instâncias da classe [BrokeredMessage][BrokeredMessage]. Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de propriedades e métodos padrão, bem como propriedades que podem ser usadas para deter propriedades personalizadas específicas da aplicação. O exemplo que se segue mostra `mytopic` como enviar cinco mensagens de teste para o tópico anteriormente criado. O `setProperty` método é usado para`MessageNumber`adicionar uma propriedade personalizada a cada mensagem. O `MessageNumber` valor da propriedade varia em cada mensagem (pode utilizar este valor para determinar quais as subscrições que a recebem, como mostra a secção [Criar uma subscrição):](#create-a-subscription)

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. Este limite superior no tamanho do tópico é de 5 GB. Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
A melhor maneira de receber mensagens `ServiceBusRestProxy->receiveSubscriptionMessage` de uma subscrição é usar um método. As mensagens podem ser recebidas em dois modos diferentes: [ *ReceiveAndDelete* e *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é a predefinição.

Na utilização do modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode), a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. O modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o Service Bus marcou a mensagem como sendo consumida, então quando a aplicação recomeça e começa a consumir mensagens novamente, perdeu a mensagem que foi consumida antes do acidente.

No modo [predefinido PeekLock,](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) receber uma mensagem torna-se uma operação de dois estágios, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação terminar de processar a mensagem (ou armazená-la de forma fiável para `ServiceBusRestProxy->deleteMessage`processamento futuro), completa a segunda fase do processo de receção passando a mensagem recebida para . Quando o Service `deleteMessage` Bus vê a chamada, marca a mensagem como sendo consumida e retira-a da fila.

O exemplo seguinte mostra como receber e processar uma mensagem utilizando o modo [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (o modo predefinido). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: lidar com falhas de aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem `unlockMessage` por alguma razão, então pode ligar para o método na mensagem recebida (em vez do `deleteMessage` método). Faz com que a Service Bus desbloqueie a mensagem dentro da fila e a disponibilize novamente para ser recebida, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem automaticamente e a faz com que a mensagem seja concluída. disponível para ser recebido novamente.

No caso de a aplicação se falhar `deleteMessage` após o processamento da mensagem, mas antes de o pedido ser emitido, a mensagem é retransmitida à aplicação quando recomeçar. Este tipo de processamento é muitas vezes chamado *pelo menos uma vez* processado; ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não puder tolerar o processamento duplicado, então os desenvolvedores de aplicações devem adicionar lógica adicional às aplicações para lidar com a entrega de mensagens duplicadas. É frequentemente alcançado `getMessageId` utilizando o método da mensagem, que permanece constante através das tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Para eliminar um tópico ou `ServiceBusRestProxy->deleteTopic` uma `ServiceBusRestProxy->deleteSubscripton` subscrição, utilize os métodos ou os métodos, respectivamente. A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico.

O exemplo que se segue `mytopic` mostra como eliminar um tópico nomeado e as suas subscrições registadas.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Ao utilizar `deleteSubscription` o método, pode eliminar uma subscrição de forma independente:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte [Filas, tópicos e subscrições.][Queues, topics, and subscriptions]

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
