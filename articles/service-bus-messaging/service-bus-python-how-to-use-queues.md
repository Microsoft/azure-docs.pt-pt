---
title: 'Quickstart: Use filas de autocarros da Azure Service com Python'
description: Este artigo mostra-lhe como usar python para criar, enviar mensagens e receber mensagens das filas do Azure Service Bus.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: a09f20b2c392dbf219750a76e9570239227dc865
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89458566"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Quickstart: Use filas de autocarros da Azure Service com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo mostra-lhe como usar python para criar, enviar mensagens e receber mensagens das filas do Azure Service Bus. 

Para obter mais informações sobre as bibliotecas python Azure Service Bus, consulte [as bibliotecas de Service Bus para Python.](/python/api/overview/azure/servicebus?view=azure-python)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os [benefícios do seu subscritor Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Um espaço de nomes de Service Bus, criado seguindo os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições](service-bus-quickstart-topics-subscriptions-portal.md). Copie a cadeia de ligação primária do ecrã de políticas de **acesso partilhado** para utilizar mais tarde neste artigo. 
- Python 3.4x ou superior, com o pacote [Python Azure Service Bus][Python Azure Service Bus package] instalado. Para mais informações, consulte o [Guia de Instalação Python.](/azure/developer/python/azure-sdk-install) 

## <a name="create-a-queue"></a>Criar uma fila

Um objeto **ServiceBusClient** permite-lhe trabalhar com filas. Para aceder programáticamente service bus, adicione a seguinte linha perto do topo do seu ficheiro Python:

```python
from azure.servicebus import ServiceBusClient
```

Adicione o seguinte código para criar um objeto **ServiceBusClient.** `<connectionstring>`Substitua-o pelo valor da cadeia de ligação primária do Service Bus. Pode encontrar este valor de acordo com **as políticas de acesso partilhado** no seu espaço de nomes de Service Bus no portal [Azure.][Azure portal]

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

O seguinte código utiliza o `create_queue` método do **ServiceBusClient** para criar uma fila com `taskqueue` configurações predefinidos:

```python
sb_client.create_queue("taskqueue")
```

Pode utilizar opções para substituir as definições de fila predefinidos, como o tempo de mensagem para viver (TTL) ou o tamanho máximo do tópico. O seguinte código cria uma fila chamada `taskqueue` com um tamanho máximo de fila de 5 GB e valor TTL de 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de ônibus de serviço, uma aplicação chama o `send` método no objeto **ServiceBusClient.** O exemplo de código a seguir cria um cliente de fila e envia uma mensagem de teste para a `taskqueue` fila. `<connectionstring>`Substitua-o pelo valor da cadeia de ligação primária do Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites e quotas do tamanho da mensagem

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que uma fila pode conter, mas há um limite no tamanho total das mensagens que a fila mantém. Pode definir o tamanho da fila na hora da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

O cliente da fila recebe mensagens de uma fila utilizando o `get_receiver` método no objeto **ServiceBusClient.** O exemplo de código a seguir cria um cliente de fila e recebe uma mensagem da `taskqueue` fila. `<connectionstring>`Substitua-o pelo valor da cadeia de ligação primária do Service Bus. 

```python
from azure.servicebus import QueueClient

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

O parâmetro opcional `peek_lock` de determinar se o Service Bus elimina as `get_receiver` mensagens da fila à medida que são lidas. O modo predefinido para receber mensagens é *PeekLock*, ou `peek_lock` definido para **True**, que lê (espreita) e bloqueia mensagens sem as apagar da fila. Cada mensagem deve então ser explicitamente completada para removê-la da fila.

Para eliminar as mensagens da fila à medida que são lidas, pode definir o `peek_lock` parâmetro de `get_receiver` **"Falso".** Eliminar mensagens como parte da operação de receção é o modelo mais simples, mas só funciona se a aplicação puder tolerar mensagens em falta se houver uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emite um pedido de receção e, em seguida, cai antes de o processar. Se a mensagem foi apagada ao ser recebida, quando a aplicação reinicia e volta a consumir mensagens, perdeu a mensagem que recebeu antes do acidente.

Se a sua aplicação não pode tolerar mensagens perdidas, receber é uma operação em duas fases. O PeekLock encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e a devolve à aplicação. Após o processamento ou armazenamento da mensagem, a aplicação completa a segunda fase do processo de receção, chamando o `complete` método no objeto **Mensagem.**  O `complete` método marca a mensagem como sendo consumida e remove-a da fila.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Manuseia falhas de aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não puder processar uma mensagem por alguma razão, pode ligar para o `unlock` método no objeto **Mensagem.** O Service Bus desbloqueia a mensagem dentro da fila e disponibiliza-a para ser novamente recebida, seja pela mesma ou por outra aplicação consumista.

Há também um tempo limite para mensagens trancadas dentro da fila. Se uma aplicação não processar uma mensagem antes do prazo de bloqueio expirar, por exemplo, se a aplicação falhar, o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

Se uma aplicação falhar após o processamento de uma mensagem, mas antes de ligar para o `complete` método, a mensagem é reentrexada na aplicação quando reinicia. Este comportamento é muitas vezes chamado *de Processamento pelo menos uma vez.* Cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o seu cenário não puder tolerar o processamento duplicado, pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega, para lidar com a entrega de mensagens duplicadas. 

> [!TIP]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite-lhe ligar-se a um espaço de nomes de Service Bus e administrar facilmente entidades de mensagens. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico das filas do Service Bus, consulte [filas, tópicos e subscrições][Queues, topics, and subscriptions] para saber mais.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
