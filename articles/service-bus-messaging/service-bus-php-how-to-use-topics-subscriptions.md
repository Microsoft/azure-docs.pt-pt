---
title: Como usar tópicos de ônibus da Azure Service com PHP
description: Neste tutorial, você aprende a usar tópicos e subscrições do Azure Service Bus a partir de uma aplicação PHP.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88066207"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Quickstart: Como usar tópicos e subscrições de Service Bus com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra-lhe como usar tópicos e subscrições do Service Bus. As amostras são escritas em PHP e utilizam o [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php). Os cenários abrangidos incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Excluir tópicos e subscrições

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu subscritor Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **espaço de nomes** de Service Bus e obter a **cadeia de ligação**.

    > [!NOTE]
    > Irá criar um **tópico** e uma **subscrição** do tópico utilizando **PHP** neste arranque rápido. 

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para a criação de uma aplicação PHP que acede ao serviço Azure Blob é as classes de referência no [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) a partir do seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação, ou Bloco de Notas.

> [!NOTE]
> A sua instalação PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e ativada.
> 
> 

Este artigo descreve como usar funcionalidades de serviço que podem ser chamadas dentro de uma aplicação PHP localmente, ou em código que funciona dentro de uma função web Azure, papel de trabalhador ou site.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de clientes Azure

### <a name="install-via-composer"></a>Instalar via Compositor
1. Crie um ficheiro nomeado **composer.jsna** raiz do seu projeto e adicione-lhe o seguinte código:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Descarregue **[compositor.phar][compositor-phar]** na raiz do seu projeto.
3. Abra um pedido de comando e execute o seguinte comando na raiz do seu projeto
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar as APIs do serviço de transporte:

1. Faça referência ao ficheiro do carregador automático utilizando a [declaração require_once.][require-once]
2. Faça referência a quaisquer aulas que possa utilizar.

O exemplo a seguir mostra como incluir o ficheiro de carregamento automático e fazer referência à classe **ServiceBusService.**

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que instalou as Bibliotecas de Clientes PHP para Azure via Compositor. Se instalou as bibliotecas manualmente ou como um pacote PEAR, deve fazer referência ao ficheiro autoloader **.php WindowsAzure.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos seguintes, a `require_once` declaração é sempre mostrada, mas apenas as classes necessárias para o exemplo executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
Para instantaneaizar um cliente service bus, você deve primeiro ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é tipicamente do `https://[yourNamespace].servicebus.windows.net` formato.

Para criar qualquer cliente de serviço Azure, tem de utilizar a `ServicesBuilder` classe. Pode:

* Passe a ligação diretamente para ele.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por defeito, vem com o apoio de uma fonte externa - variáveis ambientais.
  * Pode adicionar novas origem de dados ao expandir a classe `ConnectionStringSource`.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Você pode executar operações de gestão para tópicos de Service Bus através da `ServiceBusRestProxy` classe. Um `ServiceBusRestProxy` objeto é construído através do método de fábrica com uma cadeia de `ServicesBuilder::createServiceBusService` ligação apropriada que encapsula as permissões simbólicas para a gerir.

O exemplo a seguir mostra como instantaneamente um `ServiceBusRestProxy` e chamada para criar um tópico nomeado dentro de um espaço de `ServiceBusRestProxy->createTopic` `mytopic` `MySBNamespace` nome:

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
> Pode utilizar o `listTopics` método em `ServiceBusRestProxy` objetos para verificar se já existe um tópico com um nome especificado dentro de um espaço de nome de serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
As subscrições de tópicos também são criadas com o `ServiceBusRestProxy->createSubscription` método. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro quando uma nova subscrição for criada, o filtro **MatchAll** (predefinido) é utilizado. Quando o filtro **MatchAll** é utilizado, todas as mensagens publicadas no tópico são colocadas na fila virtual da subscrição. O exemplo a seguir cria uma subscrição com o nome `mysubscription` e utiliza o filtro **MatchAll** predefinido.

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
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica. O tipo de filtro mais flexível suportado por subscrições é o [SqlFilter,](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre sqlFilters, consulte [a SqlFilter.SqlExpression Property][sqlfilter].

> [!NOTE]
> Cada regra sobre uma subscrição processa as mensagens recebidas de forma independente, adicionando as suas mensagens de resultado à subscrição. Além disso, cada nova subscrição tem um objeto **de regra** padrão com um filtro que adiciona todas as mensagens do tópico à subscrição. Para receber apenas mensagens correspondentes ao seu filtro, tem de remover a regra predefinida. Pode remover a regra por defeito utilizando o `ServiceBusRestProxy->deleteRule` método.
> 
> 

O exemplo a seguir cria uma subscrição com o nome `HighMessages` de um **SqlFilter** que apenas seleciona mensagens que têm uma propriedade personalizada `MessageNumber` superior a 3. Consulte [Enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre a adição de propriedades personalizadas a mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Este código requer a utilização de um espaço de nome adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo` .

Da mesma forma, o exemplo a seguir cria uma subscrição com o nome `LowMessages` de uma `SqlFilter` que apenas seleciona mensagens que têm uma `MessageNumber` propriedade inferior ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada para o `mytopic` tópico, é sempre entregue aos destinatários subscritos à `mysubscription` subscrição, e entregue seletivamente aos destinatários `HighMessages` `LowMessages` subscritos e subscrições (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de Service Bus, a sua aplicação chama o `ServiceBusRestProxy->sendTopicMessage` método. O código que se segue mostra como enviar uma mensagem para o `mytopic` tópico anteriormente criado dentro do `MySBNamespace` espaço de nome de serviço.

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

As mensagens enviadas para os tópicos do Service Bus são instâncias da classe [BrokeredMessage][BrokeredMessage]. [Os objetos de Intermediação Message][BrokeredMessage] têm um conjunto de propriedades e métodos padrão, bem como propriedades que podem ser usadas para conter propriedades personalizadas específicas da aplicação. O exemplo a seguir mostra como enviar cinco mensagens de teste para o `mytopic` tópico anteriormente criado. O `setProperty` método é usado para adicionar uma propriedade personalizada `MessageNumber` () a cada mensagem. O `MessageNumber` valor da propriedade varia em cada mensagem (pode utilizar este valor para determinar quais as subscrições que a recebem, como mostrado na secção De [subscrição:](#create-a-subscription)

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. Este limite superior no tamanho do tópico é de 5 GB. Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
A melhor maneira de receber mensagens de uma subscrição é usar um `ServiceBusRestProxy->receiveSubscriptionMessage` método. As mensagens podem ser recebidas em dois modos diferentes: [ *ReceberAndDelete* e *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é a predefinição.

Na utilização do modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) * é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como a Service Bus marcou a mensagem como sendo consumida, então quando a aplicação reinicia e volta a consumir mensagens, perdeu a mensagem que foi consumida antes do acidente.

No modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) predefinido, receber uma mensagem torna-se uma operação de duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após o final da aplicação ter terminado o processamento da mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, passando a mensagem recebida para `ServiceBusRestProxy->deleteMessage` . Quando a Service Bus vê a `deleteMessage` chamada, marca a mensagem como sendo consumida e retira-a da fila.

O exemplo a seguir mostra como receber e processar uma mensagem utilizando o modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) (o modo predefinido). 

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
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não for capaz de processar a mensagem por alguma razão, então pode ligar para o `unlockMessage` método na mensagem recebida (em vez do `deleteMessage` método). Faz com que o Service Bus desbloqueie a mensagem dentro da fila e a disponibilize para ser novamente recebida, seja pela mesma aplicação consumista ou por outra aplicação consumista.

Existe também um tempo limite associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `deleteMessage` pedido ser emitido, a mensagem é reenrimida à aplicação quando reinicia. Este tipo de processamento é frequentemente chamado *pelo menos uma vez por* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o cenário não puder tolerar o processamento duplicado, os desenvolvedores de aplicações devem adicionar lógica adicional às aplicações para lidar com a entrega de mensagens duplicadas. É frequentemente conseguido usando o `getMessageId` método da mensagem, que permanece constante em todas as tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Para eliminar um tópico ou uma subscrição, utilize os `ServiceBusRestProxy->deleteTopic` métodos ou `ServiceBusRestProxy->deleteSubscripton` os métodos, respectivamente. A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico.

O exemplo a seguir mostra como eliminar um tópico nomeado `mytopic` e as suas subscrições registadas.

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

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte [filas, tópicos e subscrições.][Queues, topics, and subscriptions]

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md