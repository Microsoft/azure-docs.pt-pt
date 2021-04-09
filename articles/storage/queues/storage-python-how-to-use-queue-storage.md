---
title: Como usar o armazenamento da fila Azure da Python
description: Aprenda a utilizar o Azure Queue Storage da Python para criar e apagar filas e inserir, obter e apagar mensagens.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 8c30d05cf49162155f84961e1fd8a32361444ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653266"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Como usar o armazenamento da fila Azure da Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Descrição Geral

Este artigo demonstra cenários comuns utilizando o serviço de armazenamento de fila Azure. Os cenários abrangidos incluem inserir, espreitar, receber e apagar mensagens de fila. O código para criar e eliminar filas também está coberto.

Os exemplos deste artigo são escritos em Python e usam a biblioteca de [clientes Azure Queue Storage para Python.](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) Para obter mais informações sobre as filas, consulte a secção [etapas seguintes.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o SDK de Armazenamento do Azure para Python.

O [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requer Python v2.7, v3.3, ou mais tarde.

### <a name="install-via-pypi"></a>Instalar via PyPI

Para instalar através do Python Package Index (PyPI), escreva:

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Se estiver a atualizar a partir do Azure Storage SDK para Python v0.36 ou mais cedo, desinstale o SDK mais antigo utilizando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para obter métodos de instalação alternativos, consulte [Azure SDK para Python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Configure a sua aplicação para aceder ao Armazenamento de Filas

# <a name="python-v12"></a>[Python v12](#tab/python)

O [`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) objeto permite-lhe trabalhar com uma fila. Adicione o seguinte código perto do topo de qualquer ficheiro Python no qual deseja aceder programáticamente a uma fila Azure:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) objeto permite-lhe trabalhar com filas. O seguinte código cria um `QueueService` objeto. Adicione o seguinte código perto da parte superior de qualquer ficheiro Python no qual deseja aceder programáticamente ao Azure Storage:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

O `os` pacote fornece suporte para recuperar uma variável ambiental. O `uuid` pacote fornece suporte para gerar um identificador único para um nome de fila.

## <a name="create-a-queue"></a>Criar uma fila

A cadeia de ligação é recuperada do `AZURE_STORAGE_CONNECTION_STRING` conjunto de variáveis ambientais anteriormente.

# <a name="python-v12"></a>[Python v12](#tab/python)

O seguinte código cria um `QueueClient` objeto utilizando a cadeia de ligação de armazenamento.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O seguinte código cria um `QueueService` objeto utilizando a cadeia de ligação de armazenamento.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

As mensagens de fila Azure são armazenadas como texto. Se pretender armazenar dados binários, configurar as funções de codificação e descodição base64 antes de colocar uma mensagem na fila.

# <a name="python-v12"></a>[Python v12](#tab/python)

Configurar funções de codificação e descodificação base64 ao criar o objeto cliente.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Configure as funções de codificação e descodificação da Base64 no objeto de armazenamento de fila.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>Introduzir uma mensagem numa fila

# <a name="python-v12"></a>[Python v12](#tab/python)

Para inserir uma mensagem numa fila, utilize o [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) método.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para inserir uma mensagem numa fila, utilize o [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) método para criar uma nova mensagem e adicione-a à fila.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>Espreite as mensagens

# <a name="python-v12"></a>[Python v12](#tab/python)

Pode espreitar as mensagens sem removê-las da fila, chamando o [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) método. Por padrão, este método espreita uma única mensagem.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Pode espreitar as mensagens sem removê-las da fila, chamando o [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) método. Por padrão, este método espreita uma única mensagem.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representar uma tarefa, pode utilizar esta funcionalidade para atualizar o estado da tarefa.

# <a name="python-v12"></a>[Python v12](#tab/python)

O seguinte código utiliza o [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) método para atualizar uma mensagem. O tempo limite de visibilidade está definido para 0, o que significa que a mensagem aparece imediatamente e o conteúdo é atualizado.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O seguinte código utiliza o [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) método para atualizar uma mensagem. O tempo limite de visibilidade está definido para 0, o que significa que a mensagem aparece imediatamente e o conteúdo é atualizado.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila.

# <a name="python-v12"></a>[Python v12](#tab/python)

O [método get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) devolve propriedades de fila, incluindo o `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) método devolve propriedades de fila, incluindo. `approximate_message_count`

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

O resultado é apenas aproximado porque as mensagens podem ser adicionadas ou removidas após o serviço responder ao seu pedido.

## <a name="dequeue-messages"></a>Dequeue mensagens

Remova uma mensagem de uma fila em dois passos. Se o seu código não processar uma mensagem, este processo em duas etapas garante que pode receber a mesma mensagem e tentar novamente. Ligue `delete_message` depois de a mensagem ter sido processada com sucesso.

# <a name="python-v12"></a>[Python v12](#tab/python)

Quando ligar [para receive_messages,](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)obtém a próxima mensagem na fila por defeito. Uma mensagem devolvida `receive_messages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve [chamá delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Quando ligar [para get_messages,](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)obtém a próxima mensagem na fila por defeito. Uma mensagem devolvida `get_messages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve [chamá delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem.

# <a name="python-v12"></a>[Python v12](#tab/python)

O seguinte exemplo de código utiliza o [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) método para obter mensagens em lotes. Em seguida, processa cada mensagem dentro de cada lote utilizando um `for` laço aninhado. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O seguinte exemplo de código utiliza o [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) método para obter 16 mensagens numa única chamada. Em seguida, processa cada mensagem usando um `for` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Eliminar uma fila

# <a name="python-v12"></a>[Python v12](#tab/python)

Para eliminar uma fila e todas as mensagens contidas na sua parte, ligue para o [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) método.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para eliminar uma fila e todas as mensagens contidas na sua parte, ligue para o [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) método.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico do Armazenamento de Fila, siga estes links para saber mais.

- [Referência Azure Queue Storage Python API](/python/api/azure-storage-queue)
- [Centro de desenvolvimento python](https://azure.microsoft.com/develop/python/)
- [Referência AZure Storage REST API](/rest/api/storageservices/)
