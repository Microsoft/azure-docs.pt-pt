---
title: Use filas de ônibus de serviço Azure com python azure-servicebus versão 7.0.0
description: Este artigo mostra-lhe como usar python para enviar mensagens e receber mensagens das filas do Azure Service Bus.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 7275e33e44c20ece6eb7d620e2c1e8032be41a7b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498664"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Enviar mensagens para e receber mensagens das filas de autocarros da Azure Service (Python)
Este artigo mostra-lhe como usar python para enviar mensagens e receber mensagens das filas do Azure Service Bus. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a **cadeia de ligação** para o seu espaço de nomes de Service Bus e o nome da **fila** que criou.
- Python 2.7 ou superior, com o pacote [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) instalado. Para mais informações, consulte o [Guia de Instalação Python.](/azure/developer/python/azure-sdk-install) 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

1. Adicione a seguinte declaração de importação. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adicione as seguintes constantes. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>`Substitua-a pela cadeia de ligação para o seu espaço de nomes service bus.
    > - `<QUEUE NAME>`Substitua-o pelo nome da fila. 
3. Adicione um método para enviar uma única mensagem.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    O remetente é um objeto que atua como cliente para a fila que criou. Vai criá-lo mais tarde e enviar como argumento para esta função. 
4. Adicione um método para enviar uma lista de mensagens.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Adicione um método para enviar um lote de mensagens.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Crie um cliente de Service Bus e, em seguida, um objeto remetente de fila para enviar mensagens.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Adicione o seguinte código após a declaração de impressão. Este código recebe continuamente novas mensagens até não receber novas mensagens durante 5 ( `max_wait_time` ) segundos. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Código completo

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Executar a aplicação
Quando executar a aplicação, deverá ver a seguinte saída: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

No portal Azure, navegue para o seu espaço de nomes de Service Bus. Na página **'Visão Geral',** verifique se a conta de mensagem **recebida** e **de saída** é de 16. Se não vir os condes, refresque a página depois de esperar alguns minutos. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída":::

Selecione a fila nesta página **de visão geral** para navegar na página **de Fila de autocarros** de serviço. Também pode ver a contagem de mensagens **recebidas** e **de saída** nesta página. Também vê outras informações, como o **tamanho atual** da fila e a **contagem de mensagens ativas.** 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila":::


## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras: 

- [Biblioteca de clientes de autocarros de serviço Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Amostras.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples) 
    - A **pasta sync_samples** tem amostras que mostram como interagir com o Service Bus de forma sincronizada. Neste início rápido, usou este método. 
    - A **pasta async_samples** tem amostras que mostram como interagir com o Service Bus de forma assíncronea. 
- [documentação de referência azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true&view=azure-python-preview)