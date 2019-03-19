---
title: Como utilizar filas do Service Bus com PHP | Documentos da Microsoft
description: Saiba como utilizar as filas do Service Bus no Azure. Exemplos de código escritos em PHP.
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
ms.topic: article
ms.date: 09/10/2018
ms.author: aschhab
ms.openlocfilehash: 9915392f7bb12b31dce6e141383a48b69c6f70a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842775"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Como utilizar filas do Service Bus com PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia mostra-lhe como utilizar as filas do Service Bus. Os exemplos são escritos em PHP e a utilização a [Azure SDK para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criando filas**, **enviar e receber mensagens**, e **eliminar filas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao serviço de Blobs do Azure é a referência de classes no [Azure SDK para PHP](../php-download-sdk.md) de dentro de seu código. Pode utilizar quaisquer ferramentas de desenvolvimento para criar a sua aplicação ou o bloco de notas.

> [!NOTE]
> A instalação do PHP também tem de ter o [extensão de OpenSSL](https://php.net/openssl) instalado e ativado.
> 
> 

Neste guia, irá utilizar as funcionalidades de serviço que podem ser chamadas a partir de dentro de um aplicativo PHP localmente ou em código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar as APIs de fila do Service Bus, faça o seguinte:

1. Referenciar o ficheiro de Carregador automático com o [require_once] [ require_once] instrução.
2. Fazer referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o ficheiro de Carregador automático e a referência a `ServicesBuilder` classe.

> [!NOTE]
> Neste exemplo (e outros exemplos neste artigo) parte do princípio de que instalou as bibliotecas de cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote PEAR, tem de fazer referência a **WindowsAzure.php** Carregador automático ficheiros.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, o `require_once` instrução sempre será mostrada, mas apenas as classes necessárias para o exemplo executar são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação do Service Bus
Para criar uma instância de um cliente do Service Bus, primeiro tem de ter uma cadeia de ligação válida no seguinte formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em que `Endpoint` costuma ter o formato `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure tem de utilizar o `ServicesBuilder` classe. Pode:

* Transmita a cadeia de ligação direta para o mesmo.
* Utilize o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de ligação:
  * Por padrão ele vem com suporte para uma origem externa - variáveis de ambiente
  * Pode adicionar novas fontes, estendendo o `ConnectionStringSource` classe

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Pode efetuar operações de gestão para filas do Service Bus através do `ServiceBusRestProxy` classe. Uma `ServiceBusRestProxy` objeto é construído através do `ServicesBuilder::createServiceBusService` método de fábrica com uma cadeia de ligação adequado que encapsula as permissões de token para geri-la.

O exemplo seguinte mostra como criar uma instância de um `ServiceBusRestProxy` e chamar `ServiceBusRestProxy->createQueue` para criar uma fila com o nome `myqueue` dentro de um `MySBNamespace` espaço de nomes de serviço:

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
> Pode utilizar o `listQueues` método no `ServiceBusRestProxy` objetos para verificar se uma fila com um nome especificado já existe num espaço de nomes.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem numa fila do Service Bus, suas chamadas de aplicativo a `ServiceBusRestProxy->sendQueueMessage` método. O código seguinte mostra como enviar uma mensagem para o `myqueue` fila criada anteriormente no `MySBNamespace` espaço de nomes de serviço.

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

As mensagens enviadas para (e recebidas de) do Service Bus filas são instâncias do [BrokeredMessage] [ BrokeredMessage] classe. [BrokeredMessage] [ BrokeredMessage] objetos têm um conjunto de métodos padrão e propriedades que são utilizadas para armazenar as propriedades específicas da aplicação personalizadas e um corpo de dados arbitrários da aplicação.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. Este limite superior relativamente ao tamanho da fila é de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A melhor forma de receber mensagens de uma fila é usar um `ServiceBusRestProxy->receiveQueueMessage` método. As mensagens podem ser recebidas em dois modos diferentes: [*ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** é a predefinição.

Ao usar [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo, receber é uma operação única; ou seja, quando o Service Bus recebe um pedido de leitura para uma mensagem numa fila, marca a mensagem como consumida e devolve a mesma à aplicação. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Uma vez que o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha de sistema.

No padrão [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) modo, uma mensagem a receber torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao transmitir a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Service Bus vê a `deleteMessage` chamada, ele irá marcar a mensagem como consumida e removê-lo da fila.

O exemplo seguinte mostra como receber e processar uma mensagem usando [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) modo (o modo predefinido).

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

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o `unlockMessage` método à mensagem recebida (em vez do `deleteMessage` método). Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na fila e, se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus irá desbloquear automaticamente a mensagem e torná-lo disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a `deleteMessage` solicitação é emitida, em seguida, a mensagem será reenviada para o aplicativo quando ele for reiniciado. Isto é frequentemente chamado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não pode tolerar o processamento duplicado, em seguida, adicionar lógica adicional para aplicações para processar a entrega de mensagens duplicadas é recomendado. Isto é, frequentemente, conseguido utilizando o `getMessageId` método da mensagem, que permanece constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, veja [filas, tópicos e subscrições] [ Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, visite também os [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


