---
title: 'Quickstart: Azure Queue storage library v12 - Python'
description: Aprenda a usar a biblioteca Azure Queue Python v12 para criar uma fila e adicionar mensagens à fila. Em seguida, aprende-se a ler e a apagar mensagens da fila. Também aprenderá a apagar uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 50f15d041f9bcea66400eda0877e9d7914335d74
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345726"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Quickstart: Azure Queue storage client library v12 for Python

Começa com a versão 12 da biblioteca do cliente de armazenamento da Fila Azure para python. O armazenamento da fila Azure é um serviço para armazenar um grande número de mensagens para posterior recuperação e processamento. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes de armazenamento Azure Queue v12 para Python para:

- Criar uma fila
- Adicione mensagens a uma fila
- Espreite as mensagens em uma fila
- Atualize uma mensagem em uma fila
- Receber mensagens de uma fila
- Apagar mensagens de uma fila
- Eliminar uma fila

Recursos adicionais:

- [Documentação de referência da API](/python/api/azure-storage-queue/index)
- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-queue/)
- [Amostras](../common/storage-samples-python.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento Azure - [crie uma conta de armazenamento](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) para o seu sistema operativo - 2.7, 3.5 ou superior

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Queue v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Python chamada *queues-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Mude para o diretório *de filas-quickstart-v12 recém-criado.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instale o pacote

Instale a biblioteca do cliente de armazenamento Azure Blob para pacote Python utilizando o `pip install` comando.

```console
pip install azure-storage-queue
```

Este comando instala a biblioteca do cliente de armazenamento Azure Queue para pacote Python e todas as bibliotecas de que depende. Neste caso, esta é apenas a biblioteca central de Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

1. Abra um novo ficheiro de texto no seu editor de código
1. Adicionar `import` declarações
1. Crie a estrutura para o programa, incluindo o manuseamento de exceções muito básico

    Aqui está o código:

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

1. Guarde o novo ficheiro à medida *que queues-quickstart-v12.py* no diretório *de filas-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos. O armazenamento de fila oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama de arquitetura de armazenamento de fila](./media/storage-queues-introduction/queue1.png)

Utilize as seguintes classes Python para interagir com estes recursos:

- [QueueServiceClient](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` Permite-lhe gerir todas as filas na sua conta de armazenamento.
- [QueueClient](/python/api/azure-storage-queue/azure.storage.queue.queueclient): A `QueueClient` classe permite-lhe gerir e manipular uma fila individual e as suas mensagens.
- [FilaMessage](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): A `QueueMessage` classe representa os objetos individuais devolvidos ao chamar [receive_messages](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) numa fila.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como fazer as seguintes ações com a biblioteca de clientes de armazenamento Azure Queue para Python:

- [Obter a cadeia de ligação](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicione mensagens a uma fila](#add-messages-to-a-queue)
- [Espreite as mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualize uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber mensagens de uma fila](#receive-messages-from-a-queue)
- [Apagar mensagens de uma fila](#delete-messages-from-a-queue)
- [Eliminar uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento. A cadeia de ligação é armazenada a variável ambiente criada na secção [de cadeia de ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `try` bloco:

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

Decida um nome para a nova fila. O código abaixo anexa um valor UUID ao nome da fila para garantir que é único.

> [!IMPORTANT]
> Os nomes da fila só podem conter letras minúsculas, números e hífens, e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre o nome de filas, consulte [As Filas de Nomeação e Os Metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie um exemplo da classe [QueueClient.](/python/api/azure-storage-queue/azure.storage.queue.queueclient) Em seguida, ligue para o método [create_queue](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) para criar a fila na sua conta de armazenamento.

Adicione este código ao fim do `try` bloco:

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

### <a name="add-messages-to-a-queue"></a>Adicione mensagens a uma fila

O seguinte corte de código adiciona mensagens à fila, chamando o método [send_message.](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) Também salva a [FilaMessage](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) devolvida da terceira `send_message` chamada. `saved_message`É utilizado para atualizar o conteúdo da mensagem mais tarde no programa.

Adicione este código ao fim do `try` bloco:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Espreite as mensagens em uma fila

Espreite as mensagens na fila chamando o método [peek_messages.](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) O `peek_messages` método recupera uma ou mais mensagens da parte da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao fim do `try` bloco:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Atualize uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [update_message.](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) O `update_message` método pode alterar o tempo limite de visibilidade e o conteúdo de uma mensagem. O conteúdo da mensagem deve ser uma cadeia codificada UTF-8 que tem até 64 KB de tamanho. Juntamente com o novo conteúdo, passe em valores a partir da mensagem que foi guardada anteriormente no código. Os `saved_message` valores identificam a mensagem a atualizar.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Descarregue mensagens previamente adicionadas, chamando o método [receive_messages.](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)

Adicione este código ao fim do `try` bloco:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Apagar mensagens de uma fila

Apague as mensagens da fila depois de recebidas e processadas. Neste caso, o processamento é apenas exibindo a mensagem na consola.

A aplicação faz uma pausa para a entrada do utilizador, ligando `input` antes de processar e eliminando as mensagens. Verifique no seu [portal Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem eliminados. Quaisquer mensagens não explicitamente apagadas acabarão por se tornar visíveis na fila novamente para outra oportunidade de as processar.

Adicione este código ao fim do `try` bloco:

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

O código seguinte limpa os recursos que a app criou eliminando a fila utilizando o método [delete_queue.](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)

Adicione este código ao fim do `try` bloco e guarde o ficheiro:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria e adiciona três mensagens a uma fila Azure. O código lista as mensagens na fila e, em seguida, recupera-as e apaga-as, antes de finalmente apagar a fila.

Na janela da consola, navegue para o diretório que contém o ficheiro *queues-quickstart-v12.py* e, em seguida, execute o seguinte `python` comando para executar a aplicação.

```console
python queues-quickstart-v12.py
```

A saída da app é semelhante ao seguinte exemplo:

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

Quando a aplicação parar antes de receber mensagens, consulte a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Prima a tecla **'Inserir'** para receber e eliminar as mensagens. Quando solicitado, prima novamente a tecla **Enter** para apagar a fila e terminar a demonstração.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar uma fila e a adicionar-lhe mensagens usando o código Python. Depois aprendeu a espreitar, a recuperar e a apagar mensagens. Finalmente, aprendeu a apagar uma fila de mensagens.

Para tutoriais, amostras, arranques rápidos e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para desenvolvedores de Python](/azure/python/)

- Para saber mais, consulte as [bibliotecas de armazenamento Azure para Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
- Para ver mais aplicativos de amostra de armazenamento da Azure Queue, continue até a [Azure Queue storage v12 Python client library samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
