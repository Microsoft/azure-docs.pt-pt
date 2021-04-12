---
title: Enviar ou receber eventos de Azure Event Hubs usando Python (mais recente)
description: Este artigo fornece uma passagem para a criação de uma aplicação Python que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote azure-eventhub.
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: ea2270888830327506df5f43c18f8e798f61cc00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802782"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Enviar eventos para ou receber eventos de centros de eventos usando Python (azure-eventhub)
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote **azure-eventhub** Python.

## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Python 2.7 ou 3.6 ou mais tarde, com PIP instalado e atualizado.
- O pacote Python para Centros de Eventos. 

    Para instalar o pacote, executar este comando num pedido de comando que tem Python no seu caminho:

    ```cmd
    pip install azure-eventhub
    ```

    Instale o seguinte pacote para receber os eventos utilizando o armazenamento Azure Blob como loja de pontos de verificação:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de ligação para o espaço de nomes Do Event Hubs** seguindo as instruções do artigo: Obter a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a cadeia de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos
Nesta secção, você cria um script Python para enviar eventos para o centro de eventos que criou anteriormente.

1. Abra o seu editor python favorito, como [Visual Studio Code.](https://code.visualstudio.com/)
2. Criar um script chamado *send.py*. Este script envia um lote de eventos para o centro de eventos que criou anteriormente.
3. Cole o seguinte código em *send.py:*

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
    > Para obter o código fonte completo, incluindo comentários informativos, aceda à [página send_async.py GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Receber eventos
Este quickstart utiliza o armazenamento Azure Blob como uma loja de ponto de verificação. A loja de pontos de verificação é utilizada para persistir postos de controlo (ou seja, as últimas posições de leitura).  


> [!WARNING]
> Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blog Storage como uma loja de checkpoint, verifique a [versão API suportada do Azure Storage para a sua construção do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão no seu código. 
>
> Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte as amostras sincronizadas e [assíncronos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) no GitHub. [](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Criar uma conta de armazenamento Azure e um recipiente blob
Crie uma conta de armazenamento Azure e um recipiente de bolhas nele fazendo os seguintes passos:

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contentor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Certifique-se de que regista o fio de ligação e o nome do recipiente para posterior utilização no código de receção.


### <a name="create-a-python-script-to-receive-events"></a>Crie um script Python para receber eventos

Nesta secção, você cria um script Python para receber eventos do seu centro de eventos:

1. Abra o seu editor python favorito, como [Visual Studio Code.](https://code.visualstudio.com/)
2. Criar um script chamado *recv.py*.
3. Cole o seguinte código em *recv.py:*

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
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informativos adicionais, aceda à [página recv_with_checkpoint_store_async.py GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Executar a aplicação recetora

Para executar o script, abra um pedido de comando que tenha Python no seu caminho, e depois executar este comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Executar o aplicativo remetente

Para executar o script, abra um pedido de comando que tenha Python no seu caminho, e depois executar este comando:

```bash
python send.py
```

A janela do recetor deve exibir as mensagens enviadas para o centro de eventos.


## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, enviaste e recebeste eventos assíncronos. Para aprender a enviar e receber eventos de forma sincronizada, vá à [página de sync_samples Do GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Para todas as amostras (sincronizadas e assíncronas) no GitHub, vá à [biblioteca de clientes do Azure Event Hubs para amostras python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
