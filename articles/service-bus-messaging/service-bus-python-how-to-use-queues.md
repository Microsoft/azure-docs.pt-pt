---
title: Como utilizar as filas do Service bus do Azure com Python | Documentos da Microsoft
description: Saiba como utilizar as filas de Azure Service Bus do Python.
services: service-bus-messaging
documentationcenter: python
author: spelluru
manager: timlt
editor: ''
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/30/2018
ms.author: spelluru
ms.openlocfilehash: afc310ce4dd373b632f183245ab427a3a65a0af6
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696773"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Como utilizar filas do Service Bus com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar as filas do Service Bus. Os exemplos são escritos em Python e utilize o [pacote de Python do Azure Service Bus][Python Azure Service Bus package]. Os cenários abrangidos incluem **criando filas, enviar e receber mensagens**, e **eliminar filas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> Para instalar o Python ou o [pacote de Python do Azure Service Bus][Python Azure Service Bus package], consulte a [guia de instalação do Python](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Criar uma fila
O **ServiceBusService** objeto permite-lhe trabalhar com as filas. Adicione o seguinte código perto da parte superior de qualquer ficheiro de Python no qual pretende aceder através de programação do Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

O código seguinte cria um **ServiceBusService** objeto. Substitua `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o seu espaço de nomes, nome da chave de acesso partilhado (SAS) de assinatura e seu valor.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Os valores para o nome da chave SAS e o valor podem ser encontrados na [portal do Azure] [ Azure portal] informações da ligação, ou no Visual Studio **propriedades** painel ao selecionar o serviço Espaço de nomes do barramento no Server Explorer (conforme mostrado na secção anterior).

```python
bus_service.create_queue('taskqueue')
```

O `create_queue` método também oferece suporte a opções adicionais, que permitem-lhe substituir as definições de fila padrão, como o tempo de live (TTL) ou o tamanho máximo da fila da mensagem. O exemplo seguinte define o tamanho máximo da fila para 5 GB e o valor TTL para 1 minuto:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem numa fila do Service Bus, suas chamadas de aplicação do `send_queue_message` método no **ServiceBusService** objeto.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome `taskqueue` usando `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre as quotas, consulte [quotas do Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas a partir de um fila com o `receive_queue_message` método no **ServiceBusService** objeto:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da fila, como eles são lidas quando o parâmetro `peek_lock` está definido como **False**. Pode ler (pré-visualização) e a mensagem de bloqueio sem eliminá-lo da fila definindo o parâmetro `peek_lock` para **True**.

O comportamento de leitura e a eliminação da mensagem como parte da operação receive é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Uma vez que o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha de sistema.

Se o `peek_lock` parâmetro estiver definido como **True**, a receção torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar o **elimine** método no **mensagem** objeto. O **eliminar** método irá marcar a mensagem como consumida e removê-lo da fila.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o **desbloquear** método no **mensagem** objeto. Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Há também um tempo limite associado à mensagem bloqueada na fila e, se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o Service Bus irá desbloquear automaticamente a mensagem e torná-lo disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a **eliminar** método é chamado, em seguida, a mensagem será reenviada para o aplicativo quando ele for reiniciado. Isto é frequentemente chamado **, pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido através da **MessageId** propriedade da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de filas do Service Bus, veja estes artigos para saber mais.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

