---
title: Como usar o armazenamento de fila do Python – armazenamento do Azure
description: Saiba como usar o serviço Fila do Azure do Python para criar e excluir filas, e inserir, obter e excluir mensagens.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/14/2018
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 1ed084bfa0cf6879983e38ac6a8c5ab57e8948a8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721359"
---
# <a name="how-to-use-queue-storage-from-python"></a>Como utilizar o Armazenamento de filas do Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar cenários comuns usando o serviço de armazenamento de filas do Azure. Os exemplos são escritos em Python e usam o [SDK do armazenamento do Microsoft Azure para Python]. Os cenários cobertos incluem **Inserir**, **inspecionar**, **obter**e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [próximas etapas].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixe e instale o SDK do armazenamento do Azure para Python

O [SDK do armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer Python 2,7, 3,3, 3,4, 3,5 ou 3,6.
 
### <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar por meio do índice de pacote do Python (PyPI), digite:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Se você estiver atualizando do SDK do armazenamento do Azure para o Python versão 0,36 ou anterior, desinstale `pip uninstall azure-storage` o SDK mais antigo usando antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, consulte [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Exibir o aplicativo de exemplo

Para exibir e executar um aplicativo de exemplo que mostra como usar o Python com filas do Azure, [consulte armazenamento do Azure: Introdução com as filas do Azure no](https://github.com/Azure-Samples/storage-queue-python-getting-started)Python. 

Para executar o aplicativo de exemplo, verifique se você instalou os `azure-storage-queue` pacotes e. `azure-storage-common`

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService** . Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o armazenamento do Azure programaticamente:

```python
from azure.storage.queue import QueueService
```

O código a seguir cria um objeto **QueueService** usando o nome da conta de armazenamento e a chave de conta. Substitua ' myaccount ' e ' MyKey ' pelo nome da conta e pela chave.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila, use o **método\_Put Message** para criar uma nova mensagem e adicioná-la à fila.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Inspecionar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **Peek\_messages** . Por padrão, **inspecionar\_mensagens** exibe uma única mensagem.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Como: Remover mensagens da fila
Seu código remove uma mensagem de uma fila em duas etapas. Ao chamar **obter\_mensagens**, você obtém a próxima mensagem em uma fila por padrão. Uma mensagem retornada de **obter\_mensagens** torna-se invisível para qualquer outra mensagem de leitura de código dessa fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar **excluir\_mensagem**. Esse processo de duas etapas para remover uma mensagem garante que quando o código não processar uma mensagem devido a uma falha de hardware ou de software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. Seu código chama **a\_mensagem de exclusão** logo após a mensagem ser processada.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O exemplo de código a seguir usa o método **Get\_messages** para obter 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código a seguir usa o método **Update\_Message** para atualizar uma mensagem. O tempo limite de visibilidade é definido como 0, o que significa que a mensagem aparece imediatamente e o conteúdo é atualizado.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **método\_obter\_metadados de fila** solicita que o serviço fila retorne metadados sobre a fila e o **approximate_message_count**. O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço fila responde à sua solicitação.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o **método\_Delete Queue** .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Próximos Passos
Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python
