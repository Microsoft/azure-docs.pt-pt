---
title: Envie ou receba eventos de Azure Event Hubs usando Python (mais recente)
description: Este artigo fornece um walkthrough para criar uma aplicação Python que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote azure-eventhub versão 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 6b16398c7c1fd53562df7e4ac8e801a8c97162f6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159442"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Envie eventos ou receba eventos de centros de eventos utilizando Python (versão azure-eventhub 5)
Este quickstart mostra como enviar eventos e receber eventos de um hub de eventos usando o pacote **azure-eventhub versão 5** Python.

> [!IMPORTANT]
> Este quickstart utiliza o mais recente pacote azure-eventhub versão 5. Para um início rápido que usa o antigo pacote azure-eventhub versão 1, consulte Enviar e receber eventos usando a [versão azure-eventhub 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- Python 2.7 ou 3.5 ou mais tarde, com PIP instalado e atualizado.
- O pacote Python para Centros de Eventos. 

    Para instalar o pacote, execute este comando num pedido de comando que tenha python no seu caminho:

    ```cmd
    pip install azure-eventhub
    ```

    Instale o seguinte pacote para receber os eventos utilizando o armazenamento Azure Blob como loja de controlo:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) Em seguida, obtenha a cadeia de **ligação para o espaço** de nome do Event Hubs seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos
Nesta secção, você cria um roteiro Python para enviar eventos para o centro de eventos que criou anteriormente.

1. Abra o seu editor favorito da Python, como [Visual Studio Code.](https://code.visualstudio.com/)
2. Crie um guião chamado *send.py.* Este guião envia um lote de eventos para o centro de eventos que criou anteriormente.
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

> [!NOTE]
> Se estiver a funcionar no Azure Stack Hub, essa plataforma poderá suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Por exemplo, se estiver a correr [na versão Azure Stack Hub 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento a versão API 2017-11-09. Para um exemplo sobre como direcionar uma versão API de armazenamento específica, consulte as [amostras sincronizadas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) e [assíncronas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) no GitHub. Para obter mais informações sobre as versões de serviço de armazenamento Azure suportadas no Azure Stack Hub, consulte o [armazenamento Do Hub De Pilha Azure: Diferenças e considerações](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Crie uma conta de armazenamento Azure e um recipiente de bolhas
Crie uma conta de armazenamento Azure e um recipiente de bolhas, fazendo os seguintes passos:

1. [Criar uma conta de Armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contentor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha a cadeia de ligação à conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Certifique-se de que regista o nome da cadeia de ligação e do recipiente para posterior utilização no código de receção.


### <a name="create-a-python-script-to-receive-events"></a>Crie um roteiro Python para receber eventos

Nesta secção, você cria um script Python para receber eventos do seu centro de eventos:

1. Abra o seu editor favorito da Python, como [Visual Studio Code.](https://code.visualstudio.com/)
2. Crie um guião chamado *recv.py.*
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


### <a name="run-the-receiver-app"></a>Executar a aplicação recetor

Para executar o script, abra um pedido de comando que tenha Python no seu caminho, e, em seguida, executar este comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Executar a aplicação de remetente

Para executar o script, abra um pedido de comando que tenha Python no seu caminho, e, em seguida, executar este comando:

```bash
python send.py
```

A janela do recetor deve apresentar as mensagens enviadas para o centro do evento.


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, enviaste e recebeste eventos sincronicamente. Para aprender a enviar e receber eventos sincronizadamente, vá à [página sync_samples GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Para todas as amostras (sincronizadas e assíncronas) no GitHub, vá à biblioteca de clientes do [Azure Event Hubs para amostras de Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
