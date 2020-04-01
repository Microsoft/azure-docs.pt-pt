---
title: 'Quickstart: Use as filas de ônibus de serviço Azure com Python'
description: Este artigo mostra-lhe como usar python para criar, enviar mensagens e receber mensagens das filas do Azure Service Bus.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478652"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Quickstart: Use as filas de ônibus de serviço Azure com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo mostra-lhe como usar python para criar, enviar mensagens e receber mensagens das filas do Azure Service Bus. 

Para mais informações sobre as bibliotecas de ônibus de serviço Python Azure, consulte [bibliotecas de ônibus de serviço para Python.](/python/api/overview/azure/servicebus?view=azure-python)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os benefícios do seu Estúdio Visual ou dos benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um espaço de nome de ônibus de serviço, criado seguindo os passos na [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições](service-bus-quickstart-topics-subscriptions-portal.md). Copie a cadeia de ligação primária do ecrã de políticas de **acesso partilhado** para utilizar mais tarde neste artigo. 
- Python 3.4x ou superior, com o pacote [de ônibus de serviço Python Azure][Python Azure Service Bus package] instalado. Para mais informações, consulte o Guia de [Instalação Python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Criar uma fila

Um objeto **ServiceBusClient** permite-lhe trabalhar com filas. Para aceder programáticamente ao Service Bus, adicione a seguinte linha perto do topo do seu ficheiro Python:

```python
from azure.servicebus import ServiceBusClient
```

Adicione o seguinte código para criar um objeto **ServiceBusClient.** Substitua-a `<connectionstring>` com o valor de cadeia de cadeia de ligação primária do autocarro de serviço. Pode encontrar este valor ao abrigo de políticas de **acesso partilhado** no seu espaço de nome sacar o seu ônibus de serviço no [portal Azure][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

O seguinte código `create_queue` utiliza o método do **ServiceBusClient** para criar uma fila com `taskqueue` definições predefinidas:

```python
sb_client.create_queue("taskqueue")
```

Pode utilizar opções para anular as definições de fila predefinidas, tais como tempo de mensagem para viver (TTL) ou tamanho máximo do tópico. O seguinte código cria `taskqueue` uma fila chamada com um tamanho máximo de fila de 5 GB e valor TTL de 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila `send` de ônibus de serviço, uma aplicação chama o método no objeto **ServiceBusClient.** O exemplo de código que se segue cria `taskqueue` um cliente de fila e envia uma mensagem de teste para a fila. Substitua-a `<connectionstring>` com o valor de cadeia de cadeia de ligação primária do autocarro de serviço. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites e quotas de tamanho da mensagem

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que uma fila pode conter, mas há um limite no tamanho total das mensagens que a fila contém. Pode definir o tamanho da fila no momento da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

O cliente da fila recebe mensagens `get_receiver` de uma fila utilizando o método no objeto **ServiceBusClient.** O exemplo de código que se segue cria `taskqueue` um cliente em fila e recebe uma mensagem da fila. Substitua-a `<connectionstring>` com o valor de cadeia de cadeia de ligação primária do autocarro de serviço. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Use o parâmetro peek_lock

O `peek_lock` parâmetro opcional `get_receiver` determina se o Service Bus elimina as mensagens da fila à medida que são lidas. O modo predefinido para receção `peek_lock` de mensagens é *PeekLock*, ou definido para **True**, que lê (espreita) e bloqueia mensagens sem as apagar da fila. Cada mensagem deve então ser explicitamente completada para removê-la da fila.

Para apagar mensagens da fila à medida que `peek_lock` são `get_receiver` lidas, pode definir o parâmetro de **Falso**. Apagar mensagens como parte da operação de receção é o modelo mais simples, mas só funciona se a aplicação puder tolerar mensagens em falta se houver uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emita um pedido de receção e, em seguida, cai antes de processá-lo. Se a mensagem foi apagada ao ser recebida, quando a aplicação recomeça e volta a consumir mensagens, perdeu a mensagem que recebeu antes do acidente.

Se a sua aplicação não tolerar mensagens perdidas, receber é uma operação em duas fases. PeekLock encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e devolve-a à aplicação. Após o processamento ou armazenamento da mensagem, a aplicação completa `complete` a segunda fase do processo de receção, ligando para o método no objeto **Mensagem.**  O `complete` método marca a mensagem como sendo consumida e retira-a da fila.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com falhas de aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar uma `unlock` mensagem por alguma razão, pode ligar para o método no objeto **Mensagem.** O Service Bus desbloqueia a mensagem dentro da fila e disponibiliza-a para ser recebida novamente, quer pela mesma aplicação ou por outra aplicação consumista.

Há também um intervalo para mensagens bloqueadas dentro da fila. Se uma aplicação não processar uma mensagem antes de expirar o prazo de bloqueio, por exemplo, se a aplicação falhar, o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser recebida novamente.

Se uma aplicação falhar após o `complete` processamento de uma mensagem, mas antes de ligar para o método, a mensagem é retransmitida à aplicação quando reinicia. Este comportamento é frequentemente chamado *de Processamento pelo menos uma vez.* Cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o seu cenário não tolerar o processamento duplicado, pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega, para lidar com a entrega de mensagens duplicadas. 

> [!TIP]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite-lhe ligar-se a um espaço de nome do Bus de Serviço e administrar facilmente entidades de mensagens. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico das filas de ônibus de serviço, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions] para saber mais.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
