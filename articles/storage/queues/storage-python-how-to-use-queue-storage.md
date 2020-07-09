---
title: Como utilizar o armazenamento da Azure Queue v2.1 da Python - Azure Storage
description: Aprenda a utilizar o serviço Azure Queue v2.1 da Python para criar e apagar filas e inserir, obter e apagar mensagens.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, tracking-python
ms.openlocfilehash: 46d144a95708ac834478871ca27763f0ebd3b201
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805247"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Como usar o armazenamento da fila Azure v2.1 da Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Este artigo demonstra cenários comuns utilizando o serviço de armazenamento Azure Queue. Os cenários abordados incluem inserir, espreitar, receber e apagar mensagens de fila, e criar e apagar filas.

> [!IMPORTANT]
> Este artigo refere-se à versão antiga da biblioteca de clientes Azure Storage para Python. Para começar com a versão mais recente, consulte [a biblioteca de clientes de armazenamento Quickstart: Azure Queue para Python](storage-quickstart-queues-python.md)

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

As amostras deste artigo são escritas em Python e utilizam o [Microsoft Azure Storage SDK para Python]. Para obter mais informações sobre as filas, consulte a secção [etapas seguintes.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o SDK de Armazenamento do Azure para Python.

O [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requer a versão Python 2.7, 3.3 ou mais tarde.
 
### <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar através do Python Package Index (PyPI), escreva:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Se estiver a atualizar a partir do Azure Storage SDK para a versão 0.36 ou mais cedo da Python, desinstale o SDK mais antigo utilizando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para obter métodos de instalação alternativos, consulte [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Ver a aplicação da amostra

Para visualizar e executar uma aplicação de amostra que mostra como usar Python com filas [Azure, consulte Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Para executar a aplicação da amostra, certifique-se de que instalou as `azure-storage-queue` embalagens e `azure-storage-common` as embalagens.

## <a name="create-a-queue"></a>Criar uma fila

O objeto [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) permite-lhe trabalhar com filas. O seguinte código cria um `QueueService` objeto. Adicione o seguinte perto do topo de qualquer ficheiro Python no qual deseja aceder programáticamente ao Azure Storage:

```python
from azure.storage.queue import QueueService
```

O código a seguir cria um `QueueService` objeto utilizando o nome da conta de armazenamento e a chave da conta. Substitua *a minha conta* e a minha *chave* pelo nome da sua conta e chave.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Introduzir uma mensagem numa fila

Para inserir uma mensagem numa fila, utilize o método [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) para criar uma nova mensagem e adicioná-la à fila.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

As mensagens de fila Azure são armazenadas como texto. Se pretender armazenar dados binários, configurar as funções de codificação e descodificação base64 no objeto de serviço de fila antes de colocar uma mensagem na fila.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Pré-visualização da mensagem seguinte

Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando o método [peek_messages.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) Por padrão, `peek_messages` espreita uma única mensagem.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Dequeue mensagens

O seu código remove uma mensagem de uma fila em dois passos. Quando ligar [para get_messages,](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)recebe a próxima mensagem numa fila por defeito. Uma mensagem devolvida `get_messages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve [chamádelete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Este processo em duas etapas de remoção de uma mensagem assegura que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tentar novamente. O seu código liga `delete_message` logo após a mensagem ter sido processada.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte exemplo de código utiliza o `get_messages` método para obter 16 mensagens numa única chamada. Em seguida, processa cada mensagem usando um loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código abaixo utiliza o método [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) para atualizar uma mensagem. O tempo limite de visibilidade está definido para 0, o que significa que a mensagem aparece imediatamente e o conteúdo é atualizado.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila. O método [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) pede ao serviço de fila que devolva metadados sobre a fila e o `approximate_message_count` . O resultado é apenas aproximado porque as mensagens podem ser adicionadas ou removidas após o serviço de fila responder ao seu pedido.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na sua parte, ligue para o método [delete_queue.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu o básico do armazenamento de filas, siga estes links para saber mais.

* [Referência Azure Queues Python API](/python/api/azure-storage-queue)
* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK para Python]: https://github.com/Azure/azure-storage-python
