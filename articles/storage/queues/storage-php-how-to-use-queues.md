---
title: Como utilizar o armazenamento de fila a partir de PHP - Armazenamento Azure
description: Aprenda a utilizar o serviço de armazenamento de fila Azure para criar e apagar filas, e inserir, obter e apagar mensagens. As amostras estão escritas em PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302966"
---
# <a name="how-to-use-queue-storage-from-php"></a>Como utilizar o Armazenamento de filas do PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Este guia mostra-lhe como realizar cenários comuns utilizando o serviço de armazenamento de fila Azure. As amostras são escritas através de aulas da Biblioteca de Clientes de [Armazenamento Azure para PHP.][download] Os cenários cobertos incluem inserir, espreitar, receber e apagar mensagens de fila, bem como criar e apagar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP

O único requisito para a criação de uma aplicação PHP que acede ao armazenamento da Fila Azure é a referenciação de classes na Biblioteca de Clientes de [Armazenamento Azure para PHP][download] a partir do seu código. Pode utilizar qualquer ferramenta de desenvolvimento para criar a sua aplicação, incluindo o Notepad.

Neste guia, utiliza as funcionalidades do serviço de armazenamento de fila que podem ser chamadas dentro de uma aplicação PHP localmente, ou em código que funciona dentro de uma aplicação web no Azure.

## <a name="get-the-azure-client-libraries"></a>Obtenha as Bibliotecas do Cliente Azure

### <a name="install-via-composer"></a>Instalar via Compositor

1. Crie um ficheiro chamado **compositor.json** na raiz do seu projeto e adicione-lhe o seguinte código:
   
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

Em alternativa, vá à Biblioteca de Clientes PHP de [Armazenamento Azure][download] no GitHub para clonar o código fonte.

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao armazenamento da fila

Para utilizar as APIs para armazenamento de fila Azure, é necessário:

1. Faça referência ao ficheiro auto-carregador utilizando a declaração [require_once.]
2. Faça referência a quaisquer aulas que possa usar.

O exemplo que se segue mostra como incluir o ficheiro auto-carregador e fazer referência à classe **QueueRestProxy.**

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Nos seguintes exemplos, a afirmação `require_once` é sempre mostrada, mas apenas as classes que são necessárias para o exemplo executar são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Criar uma ligação de armazenamento Azure

Para instantaneamente um cliente de armazenamento de fila Azure, você deve primeiro ter uma cadeia de ligação válida. O formato para a cadeia de ligação de serviço de fila é o seguinte.

Para aceder a um serviço ao vivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para aceder ao armazenamento do emulador:

```php
UseDevelopmentStorage=true
```

Para criar um cliente de serviço Azure Queue, você precisa usar a classe **QueueRestProxy.** Pode utilizar qualquer uma das seguintes técnicas:

* Passe a corda de ligação diretamente para ele.
* Utilize variáveis ambientais na sua Web App para armazenar a cadeia de ligação. Consulte o documento de configuração da [configuração da aplicação web Azure](../../app-service/configure-common.md) para configurar as cordas de ligação.
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
> Não deve confiar na sensibilidade do caso para as chaves de metadados. Todas as chaves são lidas do serviço na minúscula.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

Para adicionar uma mensagem a uma fila, utilize **o QueueRestProxy->criarMensagem**. O método leva o nome da fila, o texto da mensagem e as opções de mensagem (que são opcionais).

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

Pode espreitar uma mensagem (ou mensagens) na frente de uma fila sem a retirar da fila, chamando **queueRestProxy->peekMessages**. Por padrão, o método **peekMessage** devolve uma única mensagem, mas pode alterar esse valor utilizando o método **de >ConjuntoNumberOfMessages.**

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

O seu código remove uma mensagem de uma fila em dois passos. Primeiro, ligapara **a lista de >QueueRestProxy,** o que torna a mensagem invisível para qualquer outro código que esteja a ler a partir da fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. (Se a mensagem não for eliminada neste período de tempo, torna-se novamente visível na fila.) Para terminar de remover a mensagem da fila, deve ligar para o >eliminar a Mensagem de aeliminar o **QueueRestProxy**. Este processo em duas etapas de remoção de uma mensagem garante que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. As chamadas de código **eliminam o Message** logo após a mensagem ter sido processada.

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

Pode alterar o conteúdo de uma mensagem no lugar na fila, ligando para **a atualização queueRestProxy->.** Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte atualiza a mensagem da fila com novos conteúdos, e define o tempo de visibilidade para prolongar mais 60 segundos. Isto poupa o estado de trabalho que está associado à mensagem, e dá ao cliente mais um minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

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

## <a name="additional-options-for-de-queuing-messages"></a>Opções adicionais para mensagens de desfilar

Há duas maneiras de personalizar a recuperação de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo de tempo de visibilidade mais longo ou mais curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código que se segue utiliza o método **getMessages** para obter 16 mensagens numa chamada. Em seguida, processa cada mensagem utilizando um **para** loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

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

Pode obter uma estimativa do número de mensagens numa fila. O método **QueueRestProxy->getQueueMetadata** pede ao serviço de fila para devolver metadados sobre a fila. Ligar para o método **getApproximateMessageCount** no objeto devolvido fornece uma contagem de quantas mensagens estão numa fila. A contagem é aproximada porque as mensagens podem ser adicionadas ou removidas após o serviço de fila responder ao seu pedido.

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

Para eliminar uma fila e todas as mensagens nele, ligue para o método de apagar a fila >**Queue.**

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

* Visite a [Referência API para a Biblioteca de Clientes PHP](https://azure.github.io/azure-storage-php/) de Armazenamento Azure
* Consulte o exemplo da [Fila Avançada](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Para mais informações, consulte também o [PhP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
