---
title: 'Início rápido: biblioteca de armazenamento de fila do Azure V12-Python'
description: Saiba como usar a Biblioteca Python V12 do Azure Queue para criar uma fila e adicionar mensagens à fila. Em seguida, você aprende a ler e excluir mensagens da fila. Você também aprenderá como excluir uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: a34fdc2f6d6698f53dc2ff7fdc11d0a985b23415
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473130"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Início rápido: biblioteca de cliente de armazenamento de fila do Azure V12 para Python

Introdução à biblioteca de cliente de armazenamento de filas do Azure versão 12 para Python. O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente de armazenamento de fila do Azure V12 para Python para:

* Criar uma fila
* Adicionar mensagens a uma fila
* Inspecionar mensagens em uma fila
* Atualizar uma mensagem em uma fila
* Receber mensagens de uma fila
* Excluir mensagens de uma fila
* Eliminar uma fila

[Documentação de referência de API](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [pacote (índice de pacote do Python)](https://pypi.org/project/azure-storage-queue/) | [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) para seu sistema operacional-2,7, 3,5 ou superior

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente de armazenamento de filas do Azure V12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Python chamado *filas-início rápido-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Alterne para o diretório filas recém-criadas *-início rápido-V12* .

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instalar o pacote

Instale a biblioteca de cliente do armazenamento de BLOBs do Azure para o pacote do Python usando o comando `pip install`.

```console
pip install azure-storage-queue
```

Esse comando instala a biblioteca de cliente de armazenamento de fila do Azure para o pacote do Python e todas as bibliotecas das quais ela depende. Nesse caso, essa é apenas a biblioteca principal do Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

1. Abrir um novo arquivo de texto em seu editor de códigos
1. Adicionar instruções de `import`
1. Crie a estrutura para o programa, incluindo manipulação de exceção muito básica

    Este é o código:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Salve o novo arquivo como *Queues-QuickStart-V12.py* no diretório *Queues-QuickStart-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona. O armazenamento de filas oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de filas](./media/storage-queues-introduction/queue1.png)

Use as seguintes classes python para interagir com estes recursos:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): o `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): a classe `QueueClient` permite que você gerencie e manipule uma fila individual e suas mensagens.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): a classe `QueueMessage` representa os objetos individuais retornados ao chamar [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como fazer as seguintes ações com a biblioteca de cliente de armazenamento de filas do Azure para Python:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
* [Inspecionar mensagens em uma fila](#peek-at-messages-in-a-queue)
* [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber mensagens de uma fila](#receive-messages-from-a-queue)
* [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
* [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento. A cadeia de conexão é armazenada na variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do bloco de `try`:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor UUID ao nome da fila para garantir que seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre como nomear filas, consulte [nomeando filas e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) . Em seguida, chame o método [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) para criar a fila em sua conta de armazenamento.

Adicione este código ao final do bloco de `try`:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O trecho de código a seguir adiciona mensagens à fila chamando o método [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) . Ele também salva o [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) retornado da terceira chamada de `send_message`. O `saved_message` é usado para atualizar o conteúdo da mensagem posteriormente no programa.

Adicione este código ao final do bloco de `try`:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Inspecionar mensagens em uma fila

Inspecione as mensagens na fila chamando o método [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . O método `peek_messages` recupera uma ou mais mensagens da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final do bloco de `try`:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) . O método `update_message` pode alterar o tempo limite e o conteúdo da visibilidade de uma mensagem. O conteúdo da mensagem deve ser uma cadeia de caracteres codificada em UTF-8 que tenha até 64 KB de tamanho. Junto com o novo conteúdo, transmita valores da mensagem que foi salva anteriormente no código. Os valores de `saved_message` identificam a mensagem a ser atualizada.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe mensagens adicionadas anteriormente chamando o método [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

Adicione este código ao final do bloco de `try`:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua as mensagens da fila depois que elas forem recebidas e processadas. Nesse caso, o processamento está apenas exibindo a mensagem no console.

O aplicativo pausa a entrada do usuário chamando `input` antes de processar e excluir as mensagens. Verifique no [portal do Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem excluídos. Todas as mensagens que não forem explicitamente excluídas se tornarão visíveis na fila novamente para outra oportunidade de processá-las.

Adicione este código ao final do bloco de `try`:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Eliminar uma fila

O código a seguir limpa os recursos que o aplicativo criou excluindo a fila usando o método [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) .

Adicione esse código ao final do bloco de `try` e salve o arquivo:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila, depois as recupera e exclui, antes de excluir a fila.

Na janela do console, navegue até o diretório que contém o arquivo *Queues-QuickStart-V12.py* e execute o comando `python` a seguir para executar o aplicativo.

```console
python queues-quickstart-v12.py
```

A saída do aplicativo é semelhante ao exemplo a seguir:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Quando o aplicativo for pausado antes de receber mensagens, verifique sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Pressione a tecla **Enter** para receber e excluir as mensagens. Quando solicitado, pressione a tecla **Enter** novamente para excluir a fila e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando o código Python. Em seguida, você aprendeu a inspecionar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite:

> [!div class="nextstepaction"]
> [Azure para desenvolvedores de Python](https://docs.microsoft.com/azure/python/)

* Para saber mais, consulte as [bibliotecas de armazenamento do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
* Para ver mais aplicativos de exemplo de armazenamento de filas do Azure, vá para [armazenamento de filas do Azure V12 exemplos de biblioteca de cliente do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
