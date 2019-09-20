---
title: Como usar filas do barramento de serviço com PHP | Microsoft Docs
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
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: d958202ee42b1edec5e1b65c120536c656823ecf
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147247"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Como usar filas do barramento de serviço com PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá a criar aplicativos PHP para enviar e receber mensagens de uma fila do barramento de serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando php neste tutorial. 
3. [Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) (Azure SDK para PHP)

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para a criação de um aplicativo PHP que acessa o serviço blob do Azure é a referência de classes no [SDK do Azure para php](https://github.com/Azure/azure-sdk-for-php) de dentro do seu código. Você pode usar qualquer ferramenta de desenvolvimento para criar seu aplicativo ou bloco de notas.

> [!NOTE]
> A instalação do PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e habilitada.

Neste guia, você usará os recursos de serviço, que podem ser chamados de dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função Web do Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o barramento de serviço
Para usar as APIs de fila do barramento de serviço, faça o seguinte:

1. Referencie o arquivo de carregador automático usando a instrução [require_once][require_once] .
2. Referencie todas as classes que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência `ServicesBuilder` à classe.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que você instalou as bibliotecas de cliente PHP para o Azure por meio do Composer. Se você instalou as bibliotecas manualmente ou como um pacote de pêra, deverá fazer referência ao arquivo de carregador automático **WindowsAzure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, a `require_once` instrução sempre será mostrada, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do barramento de serviço
Para criar uma instância de um cliente do barramento de serviço, você deve primeiro ter uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em `Endpoint` que normalmente é o formato `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure, você deve `ServicesBuilder` usar a classe. Pode:

* Passe a cadeia de conexão diretamente para ela.
* Use o **CloudConfigurationManager (CCM)** para verificar várias fontes externas para a cadeia de conexão:
  * Por padrão, ele vem com suporte para uma fonte externa-variáveis de ambiente
  * Você pode adicionar novas fontes estendendo `ConnectionStringSource` a classe

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Você pode executar operações de gerenciamento para filas do barramento de serviço `ServiceBusRestProxy` por meio da classe. Um `ServiceBusRestProxy` objeto é construído por meio `ServicesBuilder::createServiceBusService` do método de fábrica com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como instanciar `ServiceBusRestProxy` um e `ServiceBusRestProxy->createQueue` chamar para criar uma fila `myqueue` chamada em `MySBNamespace` um namespace de serviço:

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
> Você pode usar o `listQueues` método em `ServiceBusRestProxy` objetos para verificar se uma fila com um nome especificado já existe em um namespace.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do barramento de serviço, seu aplicativo `ServiceBusRestProxy->sendQueueMessage` chama o método. O código a seguir mostra como enviar uma mensagem para a `myqueue` fila criada anteriormente no namespace `MySBNamespace` de serviço.

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

As mensagens enviadas para (e recebidas de) filas do barramento de serviço são instâncias da classe [BrokeredMessage][BrokeredMessage] . Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de métodos e propriedades padrão que são usados para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. Esse limite superior no tamanho da fila é de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A melhor maneira de receber mensagens de uma fila é usar um `ServiceBusRestProxy->receiveQueueMessage` método. As mensagens podem ser recebidas em dois modos diferentes: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [*Peeklock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** é a predefinição.

Ao usar o modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) , Receive é uma operação Single-Shot; ou seja, quando o barramento de serviço recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna ao aplicativo. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o barramento de serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo [Peeklock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) padrão, o recebimento de uma mensagem se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-a para impedir que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento passando a mensagem `ServiceBusRestProxy->deleteMessage`recebida para. Quando o barramento de serviço `deleteMessage` vê a chamada, ele marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir mostra como receber e processar uma mensagem usando o modo [Peeklock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (o modo padrão).

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

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o `unlockMessage` método na mensagem recebida (em vez `deleteMessage` do método). Isso fará com que o barramento de serviço desbloqueie a mensagem na fila e disponibilize-a para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que a `deleteMessage` solicitação seja emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado *de processamento de pelo menos uma vez* ; ou seja, cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, é recomendável adicionar lógica adicional aos aplicativos para lidar com a entrega de mensagem duplicada. Isso geralmente é obtido usando o `getMessageId` método da mensagem, que permanece constante nas tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions] para obter mais informações.

Para obter mais informações, visite também o [centro de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


