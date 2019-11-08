---
title: 'Início rápido: usar filas do barramento de serviço do Azure com Python'
description: Saiba como usar as filas do barramento de serviço do Azure com o Python.
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: d0f579fcd82860380f1aaa651a61c0259d075a0d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748538"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Início rápido: usar filas do barramento de serviço do Azure com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo mostra como usar o Python para criar, enviar mensagens e receber mensagens de filas do barramento de serviço do Azure. 

Para obter mais informações sobre as bibliotecas do barramento de serviço do Python do Azure, consulte [bibliotecas do barramento de serviço para Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um namespace do barramento de serviço, criado seguindo as etapas em [início rápido: Use a Portal do Azure para criar um tópico e assinaturas do barramento de serviço](service-bus-quickstart-topics-subscriptions-portal.md). Copie a cadeia de conexão primária da tela **políticas de acesso compartilhado** para usar posteriormente neste artigo. 
- Python 3.4 x ou superior, com o pacote de [barramento de serviço do Python Azure][Python Azure Service Bus package] instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/python/python-sdk-azure-install). 

## <a name="create-a-queue"></a>Criar uma fila

Um objeto **ServiceBusClient** permite que você trabalhe com filas. Para acessar programaticamente o barramento de serviço, adicione a seguinte linha próxima à parte superior do seu arquivo Python:

```python
from azure.servicebus import ServiceBusClient
```

Adicione o código a seguir para criar um objeto **ServiceBusClient** . Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do barramento de serviço. Você pode encontrar esse valor em **políticas de acesso compartilhado** no namespace do barramento de serviço no [portal do Azure][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

O código a seguir usa o método `create_queue` do **ServiceBusClient** para criar uma fila chamada `taskqueue` com as configurações padrão:

```python
sb_client.create_queue("taskqueue")
```

Você pode usar opções para substituir as configurações de fila padrão, como TTL (vida útil da mensagem) ou tamanho máximo do tópico. O código a seguir cria uma fila chamada `taskqueue` com um tamanho máximo de fila de 5 GB e o valor de TTL de 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila do barramento de serviço, um aplicativo chama o método `send` no objeto **ServiceBusClient** . O exemplo de código a seguir cria um cliente de fila e envia uma mensagem de teste para a fila de `taskqueue`. Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do barramento de serviço. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Cotas e limites de tamanho de mensagem

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que uma fila pode conter, mas há uma limitação no tamanho total das mensagens que a fila contém. Você pode definir o tamanho da fila no momento da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

O cliente de fila recebe mensagens de uma fila usando o método `get_receiver` no objeto **ServiceBusClient** . O exemplo de código a seguir cria um cliente de fila e recebe uma mensagem da fila de `taskqueue`. Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do barramento de serviço. 

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

### <a name="use-the-peek_lock-parameter"></a>Usar o parâmetro peek_lock

O parâmetro opcional `peek_lock` de `get_receiver` determina se o barramento de serviço exclui as mensagens da fila à medida que elas são lidas. O modo padrão para recebimento de mensagens é *Peeklock*, ou `peek_lock` definido como **true**, que lê (exibe) e bloqueia mensagens sem excluí-las da fila. Cada mensagem deve ser explicitamente concluída para removê-la da fila.

Para excluir mensagens da fila conforme elas são lidas, você pode definir o parâmetro `peek_lock` de `get_receiver` como **false**. A exclusão de mensagens como parte da operação de recebimento é o modelo mais simples, mas só funcionará se o aplicativo puder tolerar mensagens ausentes se houver uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite uma solicitação de recebimento e depois falha antes de processá-lo. Se a mensagem foi excluída quando recebida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem recebida antes da falha.

Se seu aplicativo não puder tolerar mensagens perdidas, Receive será uma operação de duas etapas. O PeekLock localiza a próxima mensagem a ser consumida, a bloqueia para impedir que outros consumidores a recebam e a retorna ao aplicativo. Depois de processar ou armazenar a mensagem, o aplicativo conclui o segundo estágio do processo de recebimento chamando o método `complete` no objeto **Message** .  O método `complete` marca a mensagem como sendo consumida e a remove da fila.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com falhas do aplicativo e mensagens ilegível

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar uma mensagem por algum motivo, ele poderá chamar o método `unlock` no objeto **Message** . O barramento de serviço desbloqueia a mensagem na fila e a disponibiliza para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou outro.

Também há um tempo limite para mensagens bloqueadas na fila. Se um aplicativo não processar uma mensagem antes de o tempo limite de bloqueio expirar, por exemplo, se o aplicativo falhar, o barramento de serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se um aplicativo falhar após o processamento de uma mensagem, mas antes de chamar o método `complete`, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse comportamento é geralmente chamado de *processamento de pelo menos uma vez*. Cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se seu cenário não puder tolerar o processamento duplicado, você poderá usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega, para lidar com a entrega de mensagens duplicada. 

> [!TIP]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que você se conecte a um namespace do barramento de serviço e administre facilmente as entidades de mensagens. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu os conceitos básicos das filas do barramento de serviço, consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions] para saber mais.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
