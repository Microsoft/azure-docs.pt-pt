---
title: Como utilizar as filas de ônibus de serviço Azure com PHP
description: Neste tutorial, aprende-se a criar aplicações PHP para enviar mensagens e receber mensagens de uma fila de ônibus de serviço.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760696"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Quickstart: Como usar as filas de ônibus de serviço com PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, aprende-se a criar aplicações PHP para enviar mensagens e receber mensagens de uma fila de ônibus de serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nome do Ônibus de serviço usando PHP neste tutorial. 
3. [Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) (Azure SDK para PHP)

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para a criação de uma aplicação PHP que aceda ao serviço Azure Blob é a referenciação de classes no [Azure SDK para PHP](https://github.com/Azure/azure-sdk-for-php) a partir do seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação, ou Notepad.

> [!NOTE]
> A sua instalação PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e ativada.

Neste guia, utilizará funcionalidades de serviço, que podem ser chamadas de dentro de uma aplicação PHP localmente, ou em código que funciona dentro de uma função web do Azure, papel de trabalhador ou website.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de clientes Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Para utilizar as APIs de fila de ônibus de serviço, faça o seguinte:

1. Faça referência ao ficheiro auto-carregador utilizando a declaração [de require_once.][require_once]
2. Faça referência a todas as aulas que possa usar.

O exemplo que se segue mostra como incluir o ficheiro auto-carregador e fazer referência à classe `ServicesBuilder`.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) assume que instalou as Bibliotecas de Clientes PHP para o Azure via Compositor. Se instalou as bibliotecas manualmente ou como pacote PEAR, deve fazer referência ao ficheiro autoloader **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, a declaração `require_once` será sempre mostrada, mas apenas as classes necessárias para o exemplo a executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do barramento de serviço
Para instantaneamente um cliente de Ônibus de serviço, você deve primeiro ter uma cadeia de ligação válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Onde `Endpoint` é tipicamente do formato `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço Azure, você deve usar a classe `ServicesBuilder`. Pode:

* Passe a corda de ligação diretamente para ele.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por defeito vem com suporte para uma fonte externa - variáveis ambientais
  * Pode adicionar novas fontes, alargando a classe `ConnectionStringSource`

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Você pode realizar operações de gestão para filas de ônibus de serviço através da classe `ServiceBusRestProxy`. Um objeto `ServiceBusRestProxy` é construído através do método de fábrica `ServicesBuilder::createServiceBusService` com uma corda de ligação apropriada que encapsula as permissões simbólicas para geri-lo.

O exemplo que se segue mostra como instantaneamente uma `ServiceBusRestProxy` e `ServiceBusRestProxy->createQueue` para criar uma fila chamada `myqueue` dentro de um espaço de `MySBNamespace` nome de serviço:

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
> Pode utilizar o método `listQueues` sobre `ServiceBusRestProxy` objetos para verificar se já existe uma fila com um nome especificado dentro de um espaço de nome.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila de ônibus de serviço, a sua aplicação chama o método `ServiceBusRestProxy->sendQueueMessage`. O código seguinte mostra como enviar uma mensagem para a `myqueue` fila anteriormente criada dentro do espaço de nome de serviço `MySBNamespace`.

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

As mensagens enviadas para (e recebidas de) filas de ônibus de serviço são instâncias da classe [BrokeredMessage.][BrokeredMessage] Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de métodos e propriedades padrão que são usados para deter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrárias.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. Este limite superior no tamanho da fila é de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A melhor maneira de receber mensagens de uma fila é usar um método `ServiceBusRestProxy->receiveQueueMessage`. As mensagens podem ser recebidas em dois modos diferentes: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** é a predefinição.

Ao utilizar o modo [ReceiveAndDelete,](/dotnet/api/microsoft.servicebus.messaging.receivemode) receber é uma operação de tiro único; isto é, quando o Service Bus recebe um pedido de leitura de uma mensagem numa fila, marca a mensagem como sendo consumida e devolve-a à aplicação. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o Service Bus terá marcado a mensagem como sendo consumida, então quando a aplicação recomeçar e começar a consumir mensagens novamente, terá perdido a mensagem que foi consumida antes do acidente.

No modo [predefinido PeekLock,](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) receber uma mensagem torna-se uma operação de dois estágios, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando a Service Bus recebe um pedido, encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e, em seguida, a desconere na aplicação. Após a aplicação terminar de processar a mensagem (ou armazená-la de forma fiável para processamento futuro), completa a segunda fase do processo de receção, passando a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Service Bus vir a chamada `deleteMessage`, marcará a mensagem como sendo consumida e removê-la-á da fila.

O exemplo seguinte mostra como receber e processar uma mensagem utilizando o modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (o modo predefinido).

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

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por alguma razão, então pode chamar o método `unlockMessage` na mensagem recebida (em vez do método `deleteMessage`). Isto fará com que a Service Bus desbloqueie a mensagem dentro da fila e a disponibilize para ser recebida novamente, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem automaticamente e a tornará a mensagem automaticamente. disponível para ser recebido novamente.

No caso de a aplicação falhar após o processamento da mensagem, mas antes de o pedido de `deleteMessage` ser emitido, a mensagem será reentregue à aplicação quando recomeçar. Isto é frequentemente chamado de pelo *menos uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não puder tolerar o processamento duplicado, recomenda-se, em seguida, uma lógica adicional às aplicações para lidar com a entrega de mensagens duplicadas. Isto é muitas vezes conseguido utilizando o método `getMessageId` da mensagem, que permanece constante através das tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico das filas de ônibus de serviço, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions] para mais informações.

Para mais informações, visite também o [PhP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


