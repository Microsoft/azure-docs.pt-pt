---
title: Envie ou receba eventos de Azure Event Hubs usando Python (mais recente)
description: Este artigo fornece um walkthrough para criar uma aplicação Python que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote azure-eventhub versão 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906353"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Envie eventos ou receba eventos de centros de eventos utilizando Python (versão azure-eventhub 5)

O Azure Event Hubs é uma plataforma de streaming de grandes dados e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os centros de eventos podem processar e armazenar eventos, dados ou telemetria que é produzido por software e dispositivos distribuídos. Os dados enviados para um centro de eventos podem ser transformados e armazenados utilizando qualquer fornecedor de análise em tempo real ou adaptadores de lotação/armazenamento. Para mais informações, consulte as funcionalidades do [Event Hubs](event-hubs-about.md) e [do Event Hubs.](event-hubs-features.md)

Este quickstart descreve como criar aplicações Python que podem enviar eventos ou receber eventos a partir de um centro de eventos.

> [!IMPORTANT]
> Este guia de início rápido usa a versão 5 do SDK do Python de hubs de eventos do Azure. Para um início rápido que utiliza a versão 1 do Python SDK, consulte [este artigo](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa dos seguintes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um espaço de nome ative Event Hubs e centro de eventos. Para criá-las, siga as instruções na [Quickstart: Crie um centro de eventos utilizando o portal Azure](event-hubs-create.md). Grave os nomes do espaço de nome e do centro de eventos para usar mais tarde neste arranque rápido.
- O nome da chave de acesso compartilhado e o valor da chave primária para seu namespace de hubs de eventos. Obtenha o nome e o valor da chave de acesso seguindo as instruções da [Get a event hubs connection string](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave de acesso padrão é *RootManageSharedAccessKey*. Grave o nome-chave e o valor-chave principal para usar mais tarde neste arranque rápido.
- Python 2.7 ou 3.5 ou mais tarde, com PIP instalado e atualizado.
- O pacote do Python para hubs de eventos. 

    Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python em seu caminho:

    ```cmd
    pip install azure-eventhub
    ```

    Instale o seguinte pacote para receber os eventos utilizando o armazenamento Azure Blob como loja de controlo:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Enviar eventos
Nesta secção, você cria um roteiro Python para enviar eventos para o centro de eventos que criou anteriormente.

1. Abra seu editor de Python favorito, como [Visual Studio Code](https://code.visualstudio.com/).
2. Criar um script chamado *send.py*. Este guião envia um lote de eventos para o centro de eventos que criou anteriormente.
3. Colar o seguinte código em *send.py:*

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informísticos, aceda à [página GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Receber eventos
Este quickstart utiliza o armazenamento Azure Blob como uma loja de controlo. A loja de controlo é utilizada para persistir pontos de verificação (isto é, as últimas posições de leitura).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Crie uma conta de armazenamento Azure e um recipiente de bolhas
Crie uma conta de armazenamento Azure e um recipiente de bolhas, fazendo os seguintes passos:

1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obter a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Certifique-se de que regista o nome da cadeia de ligação e do recipiente para posterior utilização no código de receção.


### <a name="create-a-python-script-to-receive-events"></a>Criar um script de Python para receber eventos

Nesta seção, você cria um script Python para receber eventos do hub de eventos:

1. Abra seu editor de Python favorito, como [Visual Studio Code](https://code.visualstudio.com/).
2. Criar um script chamado *recv.py*.
3. Colar o seguinte código em *recv.py:*

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informísticos adicionais, aceda à [página GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Executar o aplicativo receptor

Para executar o script, abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute este comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Executar o aplicativo remetente

Para executar o script, abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute este comando:

```bash
python send.py
```

A janela do recetor deve apresentar as mensagens enviadas para o centro do evento.


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, enviaste e recebeste eventos sincronicamente. Para aprender a enviar e receber eventos sincronizadamente, vá à [página sync_samples GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Para todas as amostras (sincronizadas e assíncronas) no GitHub, vá à biblioteca de clientes do [Azure Event Hubs para amostras de Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
