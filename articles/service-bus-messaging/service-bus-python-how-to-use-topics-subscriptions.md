---
title: Use tópicos e subscrições de ônibus de serviço Azure com python azure-servicebus versão 7.0.0
description: Este artigo mostra-lhe como usar Python para enviar mensagens para um tópico e receber mensagens de subscrição.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4eba3ea055e78888d482927fa6eed5c7d41fa0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630052"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Envie mensagens para um tópico do Azure Service Bus e receba mensagens de subscrições ao tema (Python)
Este artigo mostra-lhe como usar python para enviar mensagens um tópico de Service Bus e receber mensagens de uma subscrição do tópico. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tema](service-bus-quickstart-topics-subscriptions-portal.md). Note a cadeia de ligação, o nome do tópico e um nome de subscrição. Usará apenas uma subscrição para este arranque rápido. 
- Python 2.7 ou superior, com o pacote [Azure Python SDK][Azure Python] instalado. Para mais informações, consulte o [Guia de Instalação Python.](/azure/developer/python/azure-sdk-install)

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

1. Adicione a seguinte declaração de importação. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adicione as seguintes constantes. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>`Substitua-a pela cadeia de ligação para o seu espaço de identificação.
    > - `<TOPIC NAME>`Substitua-o pelo nome do tópico.
    > - `<SUBSCRIPTION NAME>`Substitua-o pelo nome da subscrição do tópico. 
3. Adicione um método para enviar uma única mensagem.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    O remetente é um objeto que atua como cliente para o tópico que criou. Vai criá-lo mais tarde e enviar como argumento para esta função. 
4. Adicione um método para enviar uma lista de mensagens.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Crie um cliente de Service Bus e, em seguida, um objeto de remetente tópico para enviar mensagens.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
Adicione o seguinte código após a declaração de impressão. Este código recebe continuamente novas mensagens até não receber novas mensagens durante 5 ( `max_wait_time` ) segundos. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Código completo

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
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

Selecione o tópico no painel inferior para ver a página **Tópico do Autocarro de Serviço** para o seu tópico. Nesta página, deverá ver três mensagens recebidas e três de saída na tabela **Mensagens.** 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída":::

Nesta página, se selecionar uma subscrição, você chega à página **de Subscrição de Serviço Bus.** Pode ver a contagem de mensagens ativas, a contagem de mensagens mortas e muito mais nesta página. Neste exemplo, todas as mensagens foram recebidas, pelo que a contagem de mensagens ativas é zero. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas":::

Se comentar o código de receção, verá a contagem de mensagens ativas como 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Contagem de mensagens ativas - sem receber":::

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras: 

- [Biblioteca de clientes de autocarros de serviço Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Amostras.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples) 
    - A **pasta sync_samples** tem amostras que mostram como interagir com o Service Bus de forma sincronizada. Neste início rápido, usou este método. 
    - A **pasta async_samples** tem amostras que mostram como interagir com o Service Bus de forma assíncronea. 
- [documentação de referência azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)