---
title: Como utilizar os tópicos do Service Bus com PHP | Documentos da Microsoft
description: Saiba como utilizar tópicos do Service Bus com o PHP no Azure.
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
ms.topic: article
ms.date: 09/06/2018
ms.author: aschhab
ms.openlocfilehash: 44601d069f8c93e6101b5856e49ecf8c4b519e78
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569081"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Como utilizar tópicos do Service Bus e as subscrições com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra-lhe como utilizar tópicos do Service Bus e as subscrições. Os exemplos são escritos em PHP e a utilização a [Azure SDK para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens para um tópico**, **a receber mensagens de uma subscrição**, e **eliminar tópicos e subscrições**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao serviço de Blobs do Azure é fazer referência a classes no [Azure SDK para PHP](../php-download-sdk.md) de dentro de seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação ou o bloco de notas.

> [!NOTE]
> A instalação do PHP também tem de ter o [extensão de OpenSSL](http://php.net/openssl) instalado e ativado.
> 
> 

Este artigo descreve como utilizar funcionalidades de serviço que podem ser chamadas dentro de um aplicativo PHP localmente ou em código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar as APIs de barramento de serviço:

1. Referenciar o ficheiro de Carregador automático com o [require_once] [ require-once] instrução.
2. Fazer referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o ficheiro de Carregador automático e a referência a **ServiceBusService** classe.

> [!NOTE]
> Neste exemplo (e outros exemplos neste artigo) parte do princípio de que instalou as bibliotecas de cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote PEAR, tem de fazer referência a **WindowsAzure.php** Carregador automático ficheiros.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos a seguir, o `require_once` instrução sempre é mostrada, mas apenas as classes necessárias para o exemplo executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação do Service Bus
Para criar uma instância de um cliente do Service Bus, primeiro tem de ter uma cadeia de ligação válida no seguinte formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em que `Endpoint` costuma ter o formato `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure, tem de utilizar o `ServicesBuilder` classe. Pode:

* Transmita a cadeia de ligação direta para o mesmo.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por padrão, ele vem com suporte para uma origem externa - variáveis de ambiente.
  * Pode adicionar novas origem de dados ao expandir a classe `ConnectionStringSource`.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Pode efetuar operações de gestão para tópicos do Service Bus através do `ServiceBusRestProxy` classe. Uma `ServiceBusRestProxy` objeto é construído através do `ServicesBuilder::createServiceBusService` método de fábrica com uma cadeia de ligação adequado que encapsula as permissões de token para geri-la.

O exemplo seguinte mostra como criar uma instância de um `ServiceBusRestProxy` e chamar `ServiceBusRestProxy->createTopic` para criar um tópico com o nome `mytopic` dentro de um `MySBNamespace` espaço de nomes:

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
> Pode utilizar o `listTopics` método no `ServiceBusRestProxy` objetos para verificar se um tópico com um nome especificado já existe num espaço de nomes do serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
Subscrições de tópicos também são criadas com o `ServiceBusRestProxy->createSubscription` método. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se for especificado nenhum filtro quando é criada uma nova subscrição, o **MatchAll** filtro (predefinição) é usado. Quando o **MatchAll** filtro é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome "mysubscription" e utiliza a predefinição **MatchAll** filtro.

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
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica. O tipo mais flexível de filtro suportado pelas subscrições é a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre SqlFilters, consulte [propriedade Sqlexpression][sqlfilter].

> [!NOTE]
> Cada regra numa assinatura processa mensagens de entrada de forma independente, adicionando suas mensagens de resultado para a subscrição. Além disso, cada nova subscrição tem uma predefinição **regra** objeto com um filtro que adiciona todas as mensagens do tópico à subscrição. Para receber apenas as mensagens que correspondem ao filtro, tem de remover a regra predefinida. Pode remover a regra predefinida, utilizando o `ServiceBusRestProxy->deleteRule` método.
> 
> 

O exemplo seguinte cria uma subscrição com o nome `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens possuem `MessageNumber` propriedade superior a 3. Ver [enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre como adicionar propriedades personalizadas para as mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Esse código requer a utilização de um espaço de nomes adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Da mesma forma, o exemplo seguinte cria uma subscrição com o nome `LowMessages` com um `SqlFilter` que seleciona apenas as mensagens com um `MessageNumber` propriedade menor ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada para o `mytopic` tópico, será sempre entregue aos destinatários para a `mysubscription` subscrição e entregue seletivamente aos destinatários a `HighMessages` e `LowMessages` (dependendo de subscrições Quando o conteúdo de mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do Service Bus, suas chamadas de aplicativo a `ServiceBusRestProxy->sendTopicMessage` método. O código seguinte mostra como enviar uma mensagem para o `mytopic` tópico que criou anteriormente no `MySBNamespace` espaço de nomes de serviço.

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

As mensagens enviadas para tópicos do Service Bus são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage] [ BrokeredMessage] objetos têm um conjunto de propriedades padrão e métodos, bem como propriedades que podem ser utilizadas para conter as propriedades específicas do aplicativo personalizadas. O exemplo seguinte mostra como enviar cinco mensagens de teste para o `mytopic` tópico que criou anteriormente. O `setProperty` método é usado para adicionar uma propriedade personalizada (`MessageNumber`) para cada mensagem. O `MessageNumber` valor da propriedade varia em cada mensagem (pode utilizar este valor para determinar quais as subscrições recebem-la, como mostra a [criar uma subscrição](#create-a-subscription) secção):

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. Este limite superior relativamente ao tamanho de tópico é 5 GB. Para obter mais informações sobre as quotas, consulte [quotas do Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
A melhor forma de receber mensagens de uma subscrição é usar um `ServiceBusRestProxy->receiveSubscriptionMessage` método. As mensagens podem ser recebidas em dois modos diferentes: [*ReceiveAndDelete* e *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é a predefinição.

Na utilização do modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode), a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * modo é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem quando ocorre uma falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus marcou a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, ele tem estado em falta a mensagem consumida antes da falha de sistema.

No padrão [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) modo, uma mensagem a receber torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao transmitir a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Service Bus vê a `deleteMessage` chamada, ela marca a mensagem como consumida e removê-lo da fila.

O exemplo seguinte mostra como receber e processar uma mensagem usando [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) modo (o modo predefinido). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: lidar com falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o `unlockMessage` método à mensagem recebida (em vez do `deleteMessage` método). Ele faz com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na fila e, se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus desbloqueia automaticamente a mensagem e torná-lo disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a `deleteMessage` solicitação é emitida, em seguida, a mensagem é reenviada para a aplicação quando esta reiniciar. Este tipo de processamento é frequentemente designado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não pode tolerar o processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para aplicações para processar a entrega de mensagens duplicadas. É, frequentemente, conseguido utilizando o `getMessageId` método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Para eliminar um tópico ou uma subscrição, utilize o `ServiceBusRestProxy->deleteTopic` ou o `ServiceBusRestProxy->deleteSubscripton` métodos, respectivamente. A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico.

O exemplo seguinte mostra como eliminar um tópico com o nome `mytopic` e respetivas subscrições de registados.

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

Ao utilizar o `deleteSubscription` método, pode eliminar uma subscrição de forma independente:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
