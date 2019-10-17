---
title: 'Tutorial: usar filas do barramento de serviço do Azure com Python'
description: Saiba como usar as filas do barramento de serviço do Azure do Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: 69ae02ea7c0c04312dd4e64125c80384172c6528
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438106"
---
# <a name="tutorial-use-azure-service-bus-queues-with-python"></a>Tutorial: usar filas do barramento de serviço do Azure com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá a criar aplicativos Python para enviar e receber mensagens de uma fila do barramento de serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) .
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando o Python neste tutorial. 
1. Instale o Python ou o [pacote de barramento de serviço do Python Azure][Python Azure Service Bus package], consulte o [Guia de instalação do Python](/azure/python/python-sdk-azure-install). Consulte a documentação completa do SDK do Python do barramento de serviço [aqui](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusClient** permite que você trabalhe com filas. Adicione o código a seguir próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o barramento de serviço programaticamente:

```python
from azure.servicebus import ServiceBusClient
```

O código a seguir cria um objeto **ServiceBusClient** . Substitua `<CONNECTION STRING>` pela ConnectionString do ServiceBus.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Os valores para o nome e o valor da chave SAS podem ser encontrados no [portal do Azure][Azure portal] informações de conexão ou no painel **Propriedades** do Visual Studio ao selecionar o namespace do barramento de serviço no Gerenciador de servidores (conforme mostrado na seção anterior).

```python
sb_client.create_queue("taskqueue")
```

O método `create_queue` também dá suporte a opções adicionais, que permitem que você substitua as configurações de fila padrão, como TTL (vida útil da mensagem) ou tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e o valor de TTL como 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do barramento de serviço, seu aplicativo chama o método `send` no objeto `ServiceBusClient`.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila chamada `taskqueue` usando `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `get_receiver` no objeto `ServiceBusService`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).


As mensagens são excluídas da fila, pois são lidas quando o parâmetro `peek_lock` é definido como **false**. Você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila definindo o parâmetro `peek_lock` como **true**.

O comportamento de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o barramento de serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `peek_lock` for definido como **true**, o Receive se tornará uma operação de duas etapas, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método **delete** no objeto **Message** . O método **delete** marcará a mensagem como sendo consumida e a removerá da fila.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método **Unlock** no objeto **Message** . Isso fará com que o barramento de serviço desbloqueie a mensagem na fila e disponibilize-a para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete** seja chamado, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado **de processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Tal é, frequentemente, conseguido através da propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, consulte estes artigos para saber mais.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

