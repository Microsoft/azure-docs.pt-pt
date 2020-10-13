---
title: Como utilizar o armazenamento de fila a partir de PHP - Azure Storage
description: Saiba como utilizar o serviço de armazenamento da Fila Azure para criar e apagar filas e inserir, receber e apagar mensagens. As amostras estão escritas em PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: aefd03b9d0ce726e086dff96a648e5f3a6b28e6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84809209"
---
# <a name="how-to-use-queue-storage-from-php"></a>Como utilizar o Armazenamento de filas do PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Este guia mostra-lhe como realizar cenários comuns utilizando o serviço de armazenamento Azure Queue. As amostras são escritas através de aulas da Biblioteca do [Cliente de Armazenamento Azure para PHP.][download] Os cenários cobertos incluem inserir, espreitar, receber e eliminar mensagens de fila, bem como criar e apagar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP

O único requisito para a criação de uma aplicação PHP que acede ao armazenamento da Fila Azure é a referência de classes na Biblioteca do [Cliente de Armazenamento Azure para PHP][download] a partir do seu código. Pode utilizar qualquer ferramenta de desenvolvimento para criar a sua aplicação, incluindo o Notepad.

Neste guia, utiliza as funcionalidades de serviço de armazenamento de fila que podem ser chamadas dentro de uma aplicação PHP localmente, ou em código que está a decorrer dentro de uma aplicação web em Azure.

## <a name="get-the-azure-client-libraries"></a>Obtenha as Bibliotecas do Cliente Azure

### <a name="install-via-composer"></a>Instalar via Compositor

1. Crie um ficheiro nomeado **composer.jsna** raiz do seu projeto e adicione-lhe o seguinte código:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Baixe **[o compositor.phar][composer-phar]** na raiz do seu projeto.
3. Abra um pedido de comando e execute o seguinte comando na raiz do seu projeto
   
    ```
    php composer.phar install
    ```

Em alternativa, vá à Biblioteca de [Clientes PHP de Armazenamento Azure][download] no GitHub para clonar o código fonte.

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao armazenamento da fila

Para utilizar as APIs para armazenamento da fila Azure, é necessário:

1. Faça referência ao ficheiro do carregador automático utilizando a [declaração require_once.]
2. Faça referência a quaisquer aulas que possa utilizar.

O exemplo a seguir mostra como incluir o ficheiro de carregamento automático e fazer referência à classe **QueueRestProxy.**

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Nos exemplos seguintes, a `require_once` afirmação é sempre mostrada, mas apenas as classes necessárias para o exemplo executar são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Criar uma ligação de armazenamento Azure

Para instantaneaizar um cliente de armazenamento da Fila Azure, tem primeiro de ter uma cadeia de ligação válida. O formato da cadeia de ligação de serviço de fila é o seguinte.

Para aceder a um serviço ao vivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para aceder ao armazenamento do emulador:

```php
UseDevelopmentStorage=true
```

Para criar um cliente de serviço Azure Queue, tem de utilizar a classe **QueueRestProxy.** Pode utilizar qualquer uma das seguintes técnicas:

* Passe a ligação diretamente para ele.
* Utilize variáveis ambientais na sua Web App para armazenar a cadeia de ligação. Consulte o documento [de configuração de configuração de aplicações web Azure](../../app-service/configure-common.md) para configurar as cadeias de ligação.
Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila

Um objeto **QueueRestProxy** permite criar uma fila utilizando o método **createQueue.** Ao criar uma fila, pode definir opções na fila, mas não é necessário fazê-lo. (O exemplo abaixo mostra como definir metadados numa fila.)

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
> 
> 

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

Para adicionar uma mensagem a uma fila, utilize **o QueueRestProxy->createMessage**. O método tem o nome da fila, o texto da mensagem e as opções de mensagem (que são opcionais).

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
    $queueClient->createMessage("myqueue", "Hello World!");
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

Pode espreitar uma mensagem (ou mensagens) na parte da frente de uma fila sem a retirar da fila, chamando **o QueueRestProxy->espreitar**. Por predefinição, o método **peekMessage** devolve uma única mensagem, mas pode alterar esse valor utilizando o método **PeekMessagesOptions->setNumberOfMessages.**

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

O seu código remove uma mensagem de uma fila em dois passos. Em primeiro lugar, ligue para **a lista de >Desaparação de FilaRestProxy- >,** o que torna a mensagem invisível a qualquer outro código que esteja a ler a partir da fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. (Se a mensagem não for apagada neste período de tempo, torna-se visível novamente na fila.) Para terminar a remoção da mensagem da fila, deve ligar para **o QueueRestProxy->eliminar a Mensagens**. Este processo em duas etapas de remoção de uma mensagem assegura que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tentar novamente. O seu código chama **apagar A mensagens** logo após o processo da mensagem.

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

Pode alterar o conteúdo de uma mensagem no lugar na fila, ligando para a **atualização de >Desaparação de FilaRestProxy->**. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem da fila com novos conteúdos e define o tempo limite de visibilidade para prolongar mais 60 segundos. Isto salva o estado de trabalho que está associado à mensagem, e dá ao cliente mais um minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

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

## <a name="additional-options-for-de-queuing-messages"></a>Opções adicionais para des-filas de mensagens

Há duas maneiras de personalizar a recuperação de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo de visibilidade mais longo ou mais curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir utiliza o método **getMessages** para obter 16 mensagens numa única chamada. Em seguida, processa cada mensagem usando um **loop.** Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

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

Pode obter uma estimativa do número de mensagens numa fila. O método **QueueRestProxy->getQueueMetadata** pede ao serviço de fila para devolver metadados sobre a fila. Chamar o método **GetApproximateMessageCount** no objeto devolvido fornece uma contagem de quantas mensagens estão numa fila. A contagem só é aproximada porque as mensagens podem ser adicionadas ou removidas após o serviço de fila responder ao seu pedido.

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

Para eliminar uma fila e todas as mensagens nele, ligue para o método **QueueRestProxy->eliminar o método Deue.**

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

Agora que aprendeu o básico do armazenamento da Fila Azure, siga estes links para aprender sobre tarefas de armazenamento mais complexas:

* Visite a [Referência API para a Biblioteca de Clientes PHP de Armazenamento Azure](https://azure.github.io/azure-storage-php/)
* Veja o [exemplo da Fila Avançada.](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)

Para mais informações, consulte também o [Centro de Desenvolvimento php.](https://azure.microsoft.com/develop/php/)

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
