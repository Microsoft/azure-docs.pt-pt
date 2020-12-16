---
title: Como utilizar o armazenamento de fila a partir de PHP - Azure Storage
description: Saiba como utilizar o serviço de Armazenamento de Fila Azure para criar e apagar filas e inserir, receber e apagar mensagens. As amostras estão escritas em PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592029"
---
# <a name="how-to-use-queue-storage-from-php"></a>Como utilizar o armazenamento de fila saindo do PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Este guia mostra-lhe como realizar cenários comuns utilizando o serviço de armazenamento de fila Azure. As amostras são escritas através de aulas da biblioteca do [cliente Azure Storage para PHP.](https://github.com/Azure/azure-storage-php) Os cenários cobertos incluem inserir, espreitar, receber e eliminar mensagens de fila, bem como criar e apagar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP

O único requisito para a criação de uma aplicação PHP que acede ao Azure Queue Storage é a referência de classes na biblioteca de [clientes Azure Storage para PHP](https://github.com/Azure/azure-storage-php) a partir do seu código. Pode utilizar qualquer ferramenta de desenvolvimento para criar a sua aplicação, incluindo o Notepad.

Neste guia, utiliza as funcionalidades de serviço de Armazenamento de Fila que podem ser chamadas dentro de uma aplicação PHP localmente, ou em código que está a decorrer dentro de uma aplicação web em Azure.

## <a name="get-the-azure-client-libraries"></a>Obtenha as bibliotecas de clientes Azure

### <a name="install-via-composer"></a>Instalar via compositor

1. Crie um ficheiro nomeado `composer.json` na raiz do seu projeto e adicione-lhe o seguinte código:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Descarregue [`composer.phar`](https://getcomposer.org/composer.phar) na raiz do seu projeto.

3. Abra um pedido de comando e execute o seguinte comando na raiz do seu projeto:

    ```console
    php composer.phar install
    ```

Em alternativa, vá à biblioteca de [clientes PHP do Azure Storage PHP](https://github.com/Azure/azure-storage-php) no GitHub para clonar o código fonte.

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao Armazenamento de Filas

Para utilizar as APIs para armazenamento de fila Azure, você precisa:

1. Faça referência ao ficheiro do carregador automático utilizando a [`require_once`](https://www.php.net/manual/en/function.require-once.php) declaração.
2. Faça referência a quaisquer aulas que possa utilizar.

O exemplo a seguir mostra como incluir o ficheiro do carregador automático e fazer referência à `QueueRestProxy` classe.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Nos exemplos seguintes, a `require_once` afirmação é sempre mostrada, mas apenas as classes necessárias para executar o exemplo são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Criar uma ligação de armazenamento Azure

Para instantaneaizar um cliente de armazenamento de fila Azure, primeiro tem de ter uma cadeia de ligação válida. O formato da cadeia de ligação de armazenamento de fila é o seguinte.

Para aceder a um serviço ao vivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para aceder ao armazenamento do emulador:

```php
UseDevelopmentStorage=true
```

Para criar um cliente Azure Queue Storage, tem de utilizar a `QueueRestProxy` aula. Pode utilizar qualquer uma das seguintes técnicas:

- Passe a ligação diretamente para ele.
- Utilize variáveis ambientais na sua aplicação web para armazenar a cadeia de ligação. Consulte o documento [de configuração de configuração de aplicações web Azure](../../app-service/configure-common.md) para configurar as cadeias de ligação.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila

Um `QueueRestProxy` objeto permite criar uma fila utilizando o `CreateQueue` método. Ao criar uma fila, pode definir opções na fila, mas não é necessário fazê-lo. Este exemplo mostra como definir metadados numa fila.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Não deve confiar na sensibilidade do caso para as teclas de metadados. Todas as chaves são lidas do serviço em minúsculas.

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

Para adicionar uma mensagem a uma fila, use `QueueRestProxy->createMessage` . O método tem o nome da fila, o texto da mensagem e as opções de mensagem (que são opcionais).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Pré-visualização da mensagem seguinte

Pode espreitar uma ou mais mensagens na frente de uma fila sem as retirar da fila `QueueRestProxy->peekMessages` ligando. Por predefinição, o `peekMessage` método devolve uma única mensagem, mas pode alterar esse valor utilizando o `PeekMessagesOptions->setNumberOfMessages` método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Remover a mensagem seguinte da fila

O seu código remove uma mensagem de uma fila em dois passos. Primeiro, `QueueRestProxy->listMessages` ligas, o que torna a mensagem invisível para qualquer outro código que esteja a ler da fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. (Se a mensagem não for apagada neste período de tempo, torna-se visível novamente na fila.) Para terminar de remover a mensagem da fila, tem de ligar `QueueRestProxy->deleteMessage` . Este processo em duas etapas de remoção de uma mensagem assegura que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tentar novamente. O seu código liga `deleteMessage` logo após a mensagem ter sido processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila

Pode alterar o conteúdo de uma mensagem no lugar na fila ligando `QueueRestProxy->updateMessage` . Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem da fila com novos conteúdos e define o tempo limite de visibilidade para prolongar mais 60 segundos. Isto salva o estado de trabalho que está associado à mensagem, e dá ao cliente mais um minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para rastrear fluxos de trabalho em vários passos em mensagens de fila, sem ter de recomeçar desde o início se um passo de processamento falhar devido a falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desescodução de mensagens

Há duas maneiras de personalizar a recuperação de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo de visibilidade mais longo ou mais curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte exemplo de código utiliza o `getMessages` método para obter 16 mensagens numa única chamada. Em seguida, processa cada mensagem usando um `for` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Obtenha o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila. O `QueueRestProxy->getQueueMetadata` método recupera metadados sobre a fila. Chamar o `getApproximateMessageCount` método no objeto devolvido fornece uma contagem de quantas mensagens estão em fila. A contagem só é aproximada porque as mensagens podem ser adicionadas ou removidas após o Armazenamento de Fila responder ao seu pedido.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens nele, ligue para o `QueueRestProxy->deleteQueue` método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico do Azure Queue Storage, siga estes links para aprender sobre tarefas de armazenamento mais complexas:

- Visite a [referência da API para a biblioteca de clientes PHP do Azure Storage](https://azure.github.io/azure-storage-php/)
- Veja o exemplo avançado da [fila.](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)

Para mais informações, consulte o [centro de desenvolvimento PHP](https://azure.microsoft.com/develop/php/).
