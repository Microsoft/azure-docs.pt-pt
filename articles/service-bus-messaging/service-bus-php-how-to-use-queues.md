---
title: Como utilizar as filas de autocarros da Azure Service com PHP
description: Neste tutorial, aprende-se a criar aplicações PHP para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341114"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Quickstart: Como usar as filas de Service Bus com PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, aprende-se a criar aplicações PHP para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila.
    1. Leia a **visão geral** rápida das filas de **autocarros** de serviço. 
    2. Crie um espaço de nomes de **autocarro de serviço.** 
    3. Obtenha a **cadeia de ligação.** 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nomes do Service Bus usando PHP neste tutorial. 
3. [Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) (Azure SDK para PHP)

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para a criação de uma aplicação PHP que acede ao serviço Azure Blob é a referência de classes no [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) a partir do seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação, ou Bloco de Notas.

> [!NOTE]
> A sua instalação PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e ativada.

Neste guia, utilizará funcionalidades de serviço, que podem ser chamadas a partir de uma aplicação PHP localmente, ou em código que está a decorrer dentro de uma função web, papel de trabalhador ou website da Azure.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de clientes Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Para utilizar as APIs da fila de autocarros de serviço, faça o seguinte:

1. Faça referência ao ficheiro do carregador automático utilizando a [declaração require_once.][require_once]
2. Faça referência a quaisquer aulas que possa utilizar.

O exemplo a seguir mostra como incluir o ficheiro do carregador automático e fazer referência à `ServicesBuilder` classe.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que instalou as Bibliotecas de Clientes PHP para Azure via Compositor. Se instalou as bibliotecas manualmente ou como um pacote PEAR, deve fazer referência ao ficheiro autoloader **.php WindowsAzure.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, a `require_once` declaração será sempre mostrada, mas apenas as classes necessárias para o exemplo executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
Para instantaneaizar um cliente service bus, você deve primeiro ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é tipicamente do `[yourNamespace].servicebus.windows.net` formato.

Para criar qualquer cliente de serviço Azure, tem de utilizar a `ServicesBuilder` classe. Pode:

* Passe a ligação diretamente para ele.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por defeito vem com suporte para uma fonte externa - variáveis ambientais
  * Pode adicionar novas fontes estendendo a `ConnectionStringSource` classe

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Pode realizar operações de gestão para as filas de Service Bus através da `ServiceBusRestProxy` aula. Um `ServiceBusRestProxy` objeto é construído através do método de fábrica com uma cadeia de `ServicesBuilder::createServiceBusService` ligação apropriada que encapsula as permissões simbólicas para a gerir.

O exemplo a seguir mostra como instantaneaizar um `ServiceBusRestProxy` e chamar para criar uma fila nomeada dentro de um espaço de nome de `ServiceBusRestProxy->createQueue` `myqueue` `MySBNamespace` serviço:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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
> Pode utilizar o `listQueues` método em `ServiceBusRestProxy` objetos para verificar se já existe uma fila com um nome especificado dentro de um espaço com nomes.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila de ônibus de serviço, a sua aplicação chama o `ServiceBusRestProxy->sendQueueMessage` método. O código que se segue mostra como enviar uma mensagem para a `myqueue` fila previamente criada dentro do `MySBNamespace` espaço de nome de serviço.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
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

As mensagens enviadas (e recebidas das) filas de autocarros de serviço são casos da classe [BrokeredMessage.][BrokeredMessage] [Os objetos de Intermediação Message][BrokeredMessage] têm um conjunto de métodos e propriedades padrão que são usados para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrária.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. Este limite superior no tamanho da fila é de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A melhor maneira de receber mensagens de uma fila é usar um `ServiceBusRestProxy->receiveQueueMessage` método. As mensagens podem ser recebidas em dois modos diferentes: [*ReceberAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** é a predefinição.

Ao utilizar o modo [ReceiveAndDelete,](/dotnet/api/microsoft.servicebus.messaging.receivemode) receber é uma operação de tiro único; ou seja, quando a Service Bus recebe um pedido de leitura de uma mensagem numa fila, marca a mensagem como sendo consumida e devolve-a à aplicação. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o Service Bus terá marcado a mensagem como sendo consumida, então quando a aplicação recomeçar e começar a consumir mensagens novamente, terá perdido a mensagem que foi consumida antes do acidente.

No modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) predefinido, receber uma mensagem torna-se uma operação de duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando a Service Bus recebe um pedido, encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores o recebam e, em seguida, devolve-a à aplicação. Após o final da aplicação ter terminado o processamento da mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, passando a mensagem recebida para `ServiceBusRestProxy->deleteMessage` . Quando a Service Bus vir a `deleteMessage` chamada, marcará a mensagem como sendo consumida e removê-la-á da fila.

O exemplo a seguir mostra como receber e processar uma mensagem utilizando o modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (o modo predefinido).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não for capaz de processar a mensagem por alguma razão, então pode ligar para o `unlockMessage` método na mensagem recebida (em vez do `deleteMessage` método). Isto fará com que o Service Bus desbloqueie a mensagem dentro da fila e a disponibilize novamente para ser recebida, seja pela mesma aplicação consumista ou por outra aplicação consumista.

Existe também um tempo limite associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `deleteMessage` pedido ser emitido, a mensagem será reenrimida à aplicação quando recomeçar. Isto é frequentemente chamado *pelo menos uma vez o* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o cenário não puder tolerar o processamento duplicado, recomenda-se a adição de lógica adicional às aplicações para lidar com a entrega de mensagens duplicadas. Isto é muitas vezes conseguido usando o `getMessageId` método da mensagem, que permanece constante em todas as tentativas de entrega.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico das filas do Service Bus, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions] para mais informações.

Para mais informações, visite também o [Centro de Desenvolvimento php.](https://azure.microsoft.com/develop/php/)

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


