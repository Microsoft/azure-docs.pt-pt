---
title: 'Início rápido: como usar os tópicos do barramento de serviço com PHP'
description: 'Início rápido: saiba como usar os tópicos do barramento de serviço com PHP no Azure.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b55d3c93f9926b7446dadf069b37a878caa81e15
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721650"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Início rápido: como usar os tópicos e as assinaturas do barramento de serviço com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra como usar os tópicos e as assinaturas do barramento de serviço. Os exemplos são escritos em PHP e usam o [SDK do Azure para php](https://github.com/Azure/azure-sdk-for-php). Os cenários abordados incluem:

- Criando tópicos e assinaturas 
- Criando filtros de assinatura 
- Enviando mensagens para um tópico 
- Recebendo mensagens de uma assinatura
- Excluindo tópicos e assinaturas

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [início rápido: Use o portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **namespace** do barramento de serviço e obter a **cadeia de conexão**.

    > [!NOTE]
    > Você criará um **tópico** e uma **assinatura** para o tópico usando **php** neste guia de início rápido. 

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar um aplicativo PHP que acessa o serviço blob do Azure é fazer referência a classes no [SDK do Azure para php](https://github.com/Azure/azure-sdk-for-php) de dentro do seu código. Você pode usar qualquer ferramenta de desenvolvimento para criar seu aplicativo ou bloco de notas.

> [!NOTE]
> A instalação do PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e habilitada.
> 
> 

Este artigo descreve como usar recursos de serviço que podem ser chamados em um aplicativo PHP localmente ou no código em execução em uma função Web do Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure

### <a name="install-via-composer"></a>Instalar por meio do Composer
1. Crie um arquivo chamado **Composer. JSON** na raiz do seu projeto e adicione o seguinte código a ele:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Baixe **[Composer. Phar] [Composer-Phar]** na raiz do projeto.
3. Abra um prompt de comando e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Para usar as APIs do barramento de serviço:

1. Referencie o arquivo de carregador automático usando a instrução [require_once][require-once] .
2. Referencie todas as classes que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServiceBusService** .

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que você instalou as bibliotecas de cliente PHP para o Azure por meio do Composer. Se você instalou as bibliotecas manualmente ou como um pacote de pêra, deverá fazer referência ao arquivo de carregador automático **WindowsAzure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos a seguir, a instrução `require_once` é sempre mostrada, mas somente as classes necessárias para executar o exemplo são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do barramento de serviço
Para criar uma instância de um cliente do barramento de serviço, você deve primeiro ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em que `Endpoint` normalmente é o `https://[yourNamespace].servicebus.windows.net`de formato.

Para criar qualquer cliente de serviço do Azure, você deve usar a classe `ServicesBuilder`. Pode:

* Passe a cadeia de conexão diretamente para ela.
* Use o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de conexão:
  * Por padrão, ele vem com suporte para uma origem externa-variáveis de ambiente.
  * Pode adicionar novas origem de dados ao expandir a classe `ConnectionStringSource`.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Você pode executar operações de gerenciamento para tópicos do barramento de serviço por meio da classe `ServiceBusRestProxy`. Um objeto `ServiceBusRestProxy` é construído por meio do método `ServicesBuilder::createServiceBusService` Factory com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como criar uma instância de um `ServiceBusRestProxy` e chamar `ServiceBusRestProxy->createTopic` para criar um tópico chamado `mytopic` em um namespace `MySBNamespace`:

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
> Você pode usar o método `listTopics` em objetos `ServiceBusRestProxy` para verificar se um tópico com um nome especificado já existe dentro de um namespace de serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma subscrição
As assinaturas de tópico também são criadas com o método `ServiceBusRestProxy->createSubscription`. As subscrições têm um nome e podem ter um filtro opcional que restringe o conjunto de mensagens transmitidas para a fila virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **filtro matchall** (padrão) será usado. Quando o filtro **filtro matchall** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura chamada `mysubscription` e usa o filtro **filtro matchall** padrão.

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
Também pode definir filtros que permitem especificar quais as mensagens enviadas para um tópico a serem apresentadas numa subscrição de tópico específica. O tipo de filtro mais flexível com suporte das assinaturas é o [sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre hiperfiltros, consulte [Propriedade sqlfilter. Sqlexpression][sqlfilter].

> [!NOTE]
> Cada regra em uma assinatura processa mensagens de entrada de forma independente, adicionando suas mensagens de resultado à assinatura. Além disso, cada nova assinatura tem um objeto de **regra** padrão com um filtro que adiciona todas as mensagens do tópico à assinatura. Para receber apenas as mensagens que correspondem ao filtro, você deve remover a regra padrão. Você pode remover a regra padrão usando o método `ServiceBusRestProxy->deleteRule`.
> 
> 

O exemplo a seguir cria uma assinatura chamada `HighMessages` com um **sqlfilter** que seleciona apenas as mensagens que têm uma propriedade de `MessageNumber` personalizada maior que 3. Consulte [enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre como adicionar propriedades personalizadas a mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Esse código requer o uso de um namespace adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Da mesma forma, o exemplo a seguir cria uma assinatura chamada `LowMessages` com um `SqlFilter` que seleciona apenas as mensagens que têm uma propriedade `MessageNumber` menor ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada para o tópico `mytopic`, ela é sempre entregue aos destinatários inscritos na assinatura do `mysubscription` e entregue seletivamente aos destinatários inscritos nas assinaturas `HighMessages` e `LowMessages` (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do barramento de serviço, seu aplicativo chama o método `ServiceBusRestProxy->sendTopicMessage`. O código a seguir mostra como enviar uma mensagem para o tópico de `mytopic` criado anteriormente dentro do namespace do serviço de `MySBNamespace`.

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

As mensagens enviadas para os tópicos do Service Bus são instâncias da classe [BrokeredMessage][BrokeredMessage]. Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de propriedades e métodos padrão, bem como propriedades que podem ser usadas para manter Propriedades personalizadas específicas do aplicativo. O exemplo a seguir mostra como enviar cinco mensagens de teste para o tópico de `mytopic` criado anteriormente. O método `setProperty` é usado para adicionar uma propriedade personalizada (`MessageNumber`) a cada mensagem. O valor da propriedade `MessageNumber` varia em cada mensagem (você pode usar esse valor para determinar quais assinaturas o receberão, conforme mostrado na seção [criar uma assinatura](#create-a-subscription) ):

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

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. Esse limite superior do tamanho do tópico é 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
A melhor maneira de receber mensagens de uma assinatura é usar um método `ServiceBusRestProxy->receiveSubscriptionMessage`. As mensagens podem ser recebidas em dois modos diferentes: [ *ReceiveAndDelete* e *Peeklock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é a predefinição.

Na utilização do modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode), a receção é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa subscrição, aquele marca a mensagem como consumida e devolve a mesma à aplicação. O modo [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando ocorre uma falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem que foi consumida antes da falha.

No modo [Peeklock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) padrão, o recebimento de uma mensagem se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento, passando a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o barramento de serviço vê a chamada de `deleteMessage`, ele marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir mostra como receber e processar uma mensagem usando o modo [Peeklock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (o modo padrão). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como manipular falhas do aplicativo e mensagens ilegível
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlockMessage` na mensagem recebida (em vez do método `deleteMessage`). Ele faz com que o barramento de serviço desbloqueie a mensagem dentro da fila e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que a solicitação de `deleteMessage` seja emitida, a mensagem será entregue novamente ao aplicativo quando ele for reiniciado. Esse tipo de processamento é geralmente chamado *de processamento de pelo menos uma vez* ; ou seja, cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica adicional aos aplicativos para lidar com a entrega de mensagens duplicadas. Geralmente, é possível usar o método `getMessageId` da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Para excluir um tópico ou uma assinatura, use os métodos `ServiceBusRestProxy->deleteTopic` ou `ServiceBusRestProxy->deleteSubscripton`, respectivamente. A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico.

O exemplo a seguir mostra como excluir um tópico chamado `mytopic` e suas assinaturas registradas.

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

Usando o método `deleteSubscription`, você pode excluir uma assinatura de forma independente:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
