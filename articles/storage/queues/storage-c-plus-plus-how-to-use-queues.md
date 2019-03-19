---
title: Como utilizar o armazenamento de filas (C++) | Documentos da Microsoft
description: Saiba como utilizar o serviço de armazenamento de filas no Azure. Exemplos são escritos em C++.
services: storage
author: cbrooksmsft
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.subservice: queues
ms.openlocfilehash: 08c865e5383e4095a22aa6c10c8b181f916d1d30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999416"
---
# <a name="how-to-use-queue-storage-from-c"></a>Como utilizar o armazenamento de filas do C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia irá mostrar como realizar cenários comuns utilizando o serviço de armazenamento de filas do Azure. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **inserindo**, **observação**, **introdução**, e **a eliminar** fila de mensagens, que  **criar e eliminar filas**.

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A versão recomendada é Storage Client Library 2.2.0, que está disponível através de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, irá utilizar recursos de armazenamento que podem ser executados dentro de um aplicativo C++.

Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções na [biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.
* **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de Pacotes NuGet >Consola do Gestor de Pacotes**. Escreva o seguinte comando para o [consola de Gestor de pacotes NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de fila
Adicione que declarações na parte superior do ficheiro de C++ em que pretende utilizar as APIs de armazenamento do Azure para aceder a filas de incluir o seguinte:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Quando em execução num aplicativo cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da sua conta de armazenamento e a chave de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. Para obter informações sobre contas de armazenamento e as chaves de acesso, consulte [sobre contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar a sua aplicação no seu computador Windows local, pode utilizar o Microsoft Azure [emulador de armazenamento](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) que é instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços de BLOBs, filas e tabelas disponíveis no Azure no seu computador de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento do Azure, selecione o **começar** botão ou prima o **Windows** chave. Comece a escrever **emulador de armazenamento do Azure**e selecione **emulador de armazenamento do Microsoft Azure** na lista de aplicações.

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="retrieve-your-connection-string"></a>Obter a sua cadeia de ligação
Pode utilizar o **cloud_storage_account** classe para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
R **cloud_queue_client** objeto permite-lhe obter objetos de referência para filas. O código seguinte cria um **cloud_queue_client** objeto.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Utilizar o **cloud_queue_client** objeto para obter uma referência para a fila que pretende utilizar. Se não existir, é possível criar a fila.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem numa fila
Para inserir uma mensagem numa fila existente, primeiro crie um novo **cloud_queue_message**. Em seguida, chame o **add_message** método. R **cloud_queue_message** podem ser criados em qualquer um uma cadeia de caracteres ou uma **bytes** matriz. Eis o código que cria uma fila (se não existir) e introduz a mensagem "Olá, Mundo":

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualização da mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peek_message** método.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que n vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Como: Remover a mensagem seguinte da fila
O código remove uma mensagem da fila em dois passos. Quando chama **get_message**, obterá a seguinte mensagem numa fila. Uma mensagem devolvida por **get_message** torna-se invisível para qualquer outro código lendo as mensagens desta fila. Para concluir a remover a mensagem da fila, também tem de chamar **delete_message**. Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. A código chama **delete_message** imediatamente após a mensagem foi processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Como: Tirar maior partido das opções adicionais para remover as mensagens da fila
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza a **get_messages** método para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem, usando uma **para** loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, então, 5 minutos após transmitido a chamada para **get_messages**, as mensagens que não foram eliminadas ficarão visíveis novamente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **download_attributes** método pede ao serviço de fila para obter os atributos de fila, incluindo a contagem de mensagens. O **approximate_message_count** método obtém o número aproximado de mensagens na fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o **delete_queue_if_exists** método no objeto de fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre o armazenamento do Azure.

* [Como utilizar o armazenamento de Blobs do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o armazenamento de tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Listar os recursos de armazenamento do Azure em C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Biblioteca de clientes de armazenamento para referência do C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
