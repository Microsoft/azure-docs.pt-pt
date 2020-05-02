---
title: 'Quickstart: Biblioteca de armazenamento Azure Queue v12 - Python'
description: Aprenda a usar a biblioteca Azure Queue Python v12 para criar uma fila e adicionar mensagens à fila. Em seguida, aprende-se a ler e a apagar mensagens da fila. Também aprenderá a apagar uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78199772"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Quickstart: Biblioteca de clientes de armazenamento azure fila v12 para Python

Inicie-se com a biblioteca de clientes de armazenamento Azure Queue versão 12 para Python. O armazenamento da Fila Azure é um serviço para armazenar um grande número de mensagens para posterior recuperação e processamento. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes de armazenamento Azure Queue v12 para Python para:

* Criar uma fila
* Adicione mensagens a uma fila
* Espreite as mensagens em uma fila
* Atualizar uma mensagem em uma fila
* Receber mensagens de uma fila
* Apagar mensagens de uma fila
* Eliminar uma fila

[Documentação de](https://docs.microsoft.com/python/api/azure-storage-queue/index) | referência API Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [(Índice de Pacote Python)](https://pypi.org/project/azure-storage-queue/) | [Samples](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento Azure - [crie uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) para o seu sistema operativo - 2.7, 3.5 ou superior

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Queue v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Python chamada *queues-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Mude para o recém-criado diretório *de filas-quickstart-v12.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instale o pacote

Instale a biblioteca de clientes de armazenamento `pip install` Azure Blob para pacote Python utilizando o comando.

```console
pip install azure-storage-queue
```

Este comando instala a biblioteca de clientes de armazenamento Azure Queue para o pacote Python e todas as bibliotecas de que depende. Neste caso, esta é apenas a biblioteca central azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o quadro da aplicação

1. Abra um novo ficheiro de texto no seu editor de código
1. Adicionar `import` declarações
1. Criar a estrutura para o programa, incluindo o tratamento de exceção muito básico

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

1. Guarde o novo ficheiro *à medida que queues-quickstart-v12.py* no diretório de *filas-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assincronicamente. O armazenamento em fila oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de fila](./media/storage-queues-introduction/queue1.png)

Utilize as seguintes aulas de Python para interagir com estes recursos:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` O permite-lhe gerir todas as filas na sua conta de armazenamento.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` A classe permite-lhe gerir e manipular uma fila individual e as suas mensagens.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` A classe representa os objetos individuais devolvidos ao chamar [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) numa fila.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código de exemplo mostram-lhe como fazer as seguintes ações com a biblioteca de clientes de armazenamento Azure Queue para Python:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicione mensagens a uma fila](#add-messages-to-a-queue)
* [Espreite as mensagens em uma fila](#peek-at-messages-in-a-queue)
* [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber mensagens de uma fila](#receive-messages-from-a-queue)
* [Apagar mensagens de uma fila](#delete-messages-from-a-queue)
* [Eliminar uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento. A cadeia de ligação é armazenada a variável ambiental criada na secção de cordas de ligação de [armazenamento Configure.](#configure-your-storage-connection-string)

Adicione este código `try` dentro do bloco:

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

Decida um nome para a nova fila. O código abaixo anexa um valor UUID para o nome da fila para garantir que é único.

> [!IMPORTANT]
> Os nomes da fila só podem conter letras minúsculas, números e hífens, e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para mais informações sobre o nome das filas, consulte [Filas de Nomeação e Metadados](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [QueueClient.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) Em seguida, ligue para o [método create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) para criar a fila na sua conta de armazenamento.

Adicione este código ao `try` fim do bloco:

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

O seguinte código de corte adiciona mensagens à fila, ligando para o método [send_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) Também guarda o [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) devolvido `send_message` da terceira chamada. O `saved_message` é utilizado para atualizar o conteúdo da mensagem mais tarde no programa.

Adicione este código ao `try` fim do bloco:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Espreite as mensagens em uma fila

Espreite as mensagens na fila chamando o método [peek_messages.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) O `peek_messages` método recupera uma ou mais mensagens da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao `try` fim do bloco:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem ligando para o método [update_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) O `update_message` método pode alterar o tempo de visibilidade e o conteúdo da mensagem. O conteúdo da mensagem deve ser uma cadeia codificada UTF-8 com até 64 KB de tamanho. Juntamente com o novo conteúdo, passe os valores da mensagem que foi guardada anteriormente no código. Os `saved_message` valores identificam qual mensagem atualizar.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Descarregue as mensagens anteriormente adicionadas, ligando para o método [receive_messages.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)

Adicione este código ao `try` fim do bloco:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Apagar mensagens de uma fila

Apague as mensagens da fila depois de serem recebidas e processadas. Neste caso, o processamento está apenas a exibir a mensagem na consola.

A aplicação faz uma pausa `input` para a entrada do utilizador, ligando antes de processar e eliminando as mensagens. Verifique no seu [portal Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem apagados. Quaisquer mensagens não explicitamente eliminadas acabarão por voltar a ser visíveis na fila para mais uma oportunidade de as processar.

Adicione este código ao `try` fim do bloco:

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

O código seguinte limpa os recursos que a app criou ao apagar a fila utilizando o método [delete_queue.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)

Adicione este código à `try` extremidade do bloco e guarde o ficheiro:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria e adiciona três mensagens a uma fila Azure. O código lista as mensagens na fila e, em seguida, recupera e elimina-as, antes de finalmente apagar a fila.

Na janela da consola, navegue *queues-quickstart-v12.py* para o diretório `python` que contém o ficheiro queues-quickstart-v12.py e, em seguida, execute o seguinte comando para executar a aplicação.

```console
python queues-quickstart-v12.py
```

A saída da aplicação é semelhante ao seguinte exemplo:

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

Quando a aplicação parar antes de receber mensagens, verifique a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Prima a tecla **Enter** para receber e apagar as mensagens. Quando solicitado, prima novamente a tecla **Enter** para apagar a fila e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma fila e a adicionar mensagens usando o código Python. Depois aprendeu a espreitar, a recuperar e a apagar mensagens. Finalmente, aprendeu a apagar uma fila de mensagens.

Para tutoriais, amostras, arranques rápidos e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para desenvolvedores de Python](https://docs.microsoft.com/azure/python/)

* Para saber mais, consulte as [bibliotecas de Armazenamento Azure para Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
* Para ver mais aplicações de armazenamento de fila Azure, continue a armazenar amostras de biblioteca de [clientes Azure Queue v12 Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)
