---
title: Como utilizar o armazenamento de filas do PHP | Documentos da Microsoft
description: Saiba como utilizar o serviço de armazenamento de filas do Azure para criar e eliminar filas, inserir, obter e eliminar mensagens. Exemplos são escritos em PHP.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 0f9cc11e64e2517ef7aff0cd51705c39bf212b37
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011424"
---
# <a name="how-to-use-queue-storage-from-php"></a>Como utilizar o Armazenamento de filas do PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar tarefas comuns com o serviço de armazenamento de filas do Azure. Os exemplos são escritos por meio de classes a partir da [biblioteca de clientes de armazenamento do Azure para PHP][download]. Os cenários abrangidos incluem a inserção, observação, obter e eliminar mensagens de filas, bem como criar e eliminar filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP
O único requisito para criar uma aplicação PHP que acede ao armazenamento de filas do Azure é a referência de classes no [biblioteca de clientes de armazenamento do Azure para PHP] [ download] de dentro de seu código. Pode utilizar qualquer ferramenta de desenvolvimento para criar a sua aplicação, incluindo o Notepad.

Neste guia, utilize as funcionalidades de serviço de armazenamento de fila que podem ser chamadas dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função da web do Azure, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure
### <a name="install-via-composer"></a>Instalar através do compositor
1. Crie um ficheiro denominado **Composer. JSON** na raiz do seu projeto e adicione o seguinte código ao mesmo:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Baixe **[composer.phar] [ composer-phar]** na raiz do projeto.
3. Abra uma linha de comandos e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

Em alternativa, vá para o [biblioteca de cliente PHP de armazenamento do Azure] [ download] no GitHub para clonar o código-fonte.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de fila
Para utilizar as APIs para o armazenamento de filas do Azure, terá de:

1. Referenciar o ficheiro de Carregador automático utilizando o [require_once] instrução.
2. Fazer referência a quaisquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o ficheiro de Carregador automático e a referência a **QueueRestProxy** classe.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Nos exemplos a seguir, o `require_once` instrução é sempre apresentada, mas apenas as classes que são necessárias para o exemplo executar são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
Para criar uma instância de um cliente de armazenamento de filas do Azure, primeiro tem de ter uma cadeia de ligação válido. Segue-se o formato para a cadeia de ligação de serviço de fila.

Para aceder a um serviço em direto:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para acessar o armazenamento de emulador:

```php
UseDevelopmentStorage=true
```

Para criar um cliente do serviço de fila do Azure, tem de utilizar o **QueueRestProxy** classe. Pode usar qualquer uma das seguintes técnicas:

* Transmita a cadeia de ligação direta para o mesmo.
* Utilize variáveis de ambiente na sua aplicação Web para armazenar a cadeia de ligação. Ver [definições de configuração de aplicação web do Azure](../../app-service/web-sites-configure.md) documento para configurar cadeias de ligação.
Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
R **QueueRestProxy** objeto permite-lhe criar uma fila com o **createQueue** método. Ao criar uma fila, pode definir as opções na fila, mas ao fazê-lo por isso, não é necessário. (O exemplo abaixo mostra como definir metadados numa fila.)

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
> Não deve confiar em maiúsculas e minúsculas para chaves de metadados. Todas as chaves são lidas a partir do serviço em minúsculas.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem numa fila
Para adicionar uma mensagem numa fila, utilize **QueueRestProxy -> createMessage**. O método usa o nome da fila, o texto da mensagem e opções de mensagem (que são opcionais).

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
Pode pré-visualizar uma mensagem (ou de mensagens) à frente de uma fila sem a remover da fila chamando **QueueRestProxy -> peekMessages**. Por predefinição, o **peekMessage** método retorna uma única mensagem, mas pode alterar esse valor usando o **PeekMessagesOptions -> setNumberOfMessages** método.

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
O código remove a mensagem da fila em dois passos. Em primeiro lugar, chama **QueueRestProxy -> listMessages**, o que torna a mensagem invisível para qualquer outro código que é de leitura da fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. (Se a mensagem não for eliminada neste período de tempo, torna-se visível na fila novamente.) Para concluir a remover a mensagem da fila, deve chamar **QueueRestProxy -> deleteMessage**. Este processo de dois passos da remoção de uma mensagem garante que quando o seu código não consegue processar uma mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente. A código chama **deleteMessage** imediatamente após a mensagem foi processada.

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
Pode alterar o conteúdo de uma mensagem no local na fila chamando **QueueRestProxy -> updateMessage**. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código a seguir atualiza a mensagem de fila com novos conteúdos e define o tempo limite de visibilidade 60 segundos. Esta ação guarda o estado do trabalho que está associada a mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

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

## <a name="additional-options-for-de-queuing-messages"></a>Opções adicionais para remover a atribuição de enfileiramento de mensagens
Existem duas formas de personalizar a obtenção de mensagens de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de visibilidade superiores ou inferiores, permitindo que seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza a **getMessages** método para obter 16 mensagens numa chamada. Em seguida, processa cada mensagem através de um **para** loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

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

## <a name="get-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **QueueRestProxy -> getQueueMetadata** método pede ao serviço de fila para devolver metadados sobre a fila. Chamar o **getApproximateMessageCount** método no objeto retornado fornece uma contagem de quantas mensagens estão numa fila. A contagem é apenas aproximada, porque as mensagens podem ser adicionadas ou removidas depois do serviço de fila responde ao seu pedido.

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
Para eliminar uma fila e todas as mensagens nela, chame o **QueueRestProxy -> deleteQueue** método.

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

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas do Azure, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas:

* Visite o [referência da API para a biblioteca de cliente PHP do armazenamento do Azure](https://azure.github.io/azure-storage-php/)
* Consulte a [exemplo de fila Advanced](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Para obter mais informações, consulte também os [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar

