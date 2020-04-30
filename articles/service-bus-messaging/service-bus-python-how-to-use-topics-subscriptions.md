---
title: 'Quickstart: Use tópicos e subscrições de ônibus de serviço Azure com Python'
description: Este artigo mostra-lhe como criar um tópico de ônibus de serviço Azure, subscrição, enviar mensagens para um tópico e receber mensagens de subscrição.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80478065"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Quickstart: Use tópicos de ônibus de serviço e subscrições com Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar python com tópicos e subscrições de ônibus de serviço Azure. As amostras utilizam o pacote [Azure Python SDK][Azure Python package] para: 

- Criar tópicos e subscrições para tópicos
- Criar filtros e regras de subscrição
- Enviar mensagens para tópicos 
- Receber mensagens de subscrições
- Eliminar tópicos e subscrições

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os benefícios do seu Estúdio Visual ou dos benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um espaço de nome de ônibus de serviço, criado seguindo os passos na [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições](service-bus-quickstart-topics-subscriptions-portal.md). Copie o nome do espaço de nome, o nome da chave de acesso partilhado e o valor chave principal do ecrã de políticas de **acesso partilhado** para usar mais tarde neste arranque rápido. 
- Python 3.4x ou superior, com o pacote [Azure Python SDK][Azure Python package] instalado. Para mais informações, consulte o Guia de [Instalação Python](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Criar um objeto ServiceBusService

Um objeto **ServiceBusService** permite-lhe trabalhar com tópicos e subscrições de tópicos. Para aceder programáticamente ao Service Bus, adicione a seguinte linha perto do topo do seu ficheiro Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adicione o seguinte código para criar um objeto **ServiceBusService.** Substitua, `<namespace>` `<sharedaccesskeyname>` `<sharedaccesskeyvalue>` e com o nome do espaço de nome do Autocarro de Serviço, nome chave Assinatura de Acesso Partilhado (SAS) e valor-chave primário. Pode encontrar estes valores ao abrigo de políticas de **acesso partilhado** no seu espaço de nome sacar o seu ônibus de serviço no [portal Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Criar um tópico

O seguinte código `create_topic` utiliza o método para `mytopic`criar um tópico de Ônibus de serviço chamado , com definições padrão:

```python
bus_service.create_topic('mytopic')
```

Pode utilizar opções de tópicos para anular as definições padrão de tópicos, tais como tempo de mensagem para viver (TTL) ou tamanho máximo do tópico. O exemplo seguinte cria `mytopic` um tópico nomeado com um tamanho máximo de tópico de 5 GB e mensagem padrão TTL de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar subscrições

Também utiliza o objeto **ServiceBusService** para criar subscrições a tópicos. Uma subscrição pode ter um filtro para restringir o conjunto de mensagens entregue à sua fila virtual. Se não especificar um filtro, novas subscrições utilizam o filtro **MatchAll** predefinido, que coloca todas as mensagens publicadas no tópico na fila virtual da subscrição. O exemplo seguinte cria `mytopic` `AllMessages` uma subscrição para nome que utiliza o filtro **MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Utilize filtros com subscrições

Utilize `create_rule` o método do objeto **ServiceBusService** para filtrar as mensagens que aparecem numa subscrição. Pode especificar regras quando criar a subscrição ou adicionar regras às subscrições existentes.

O tipo de filtro mais flexível é um **SqlFilter,** que utiliza um subconjunto de SQL-92. Os filtros SQL funcionam com base nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que pode utilizar com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Uma vez que o filtro predefinido **MatchAll** se aplica automaticamente a todas as novas subscrições, tem de removê-lo das subscrições que pretende filtrar, ou **o MatchAll** sobrepor-se-á a quaisquer outros filtros que especifique. Pode remover a regra predefinida utilizando o `delete_rule` método do objeto **ServiceBusService.**

O exemplo seguinte cria `mytopic` `HighMessages`uma subscrição para nome `HighMessageFilter`, com uma regra **SqlFilter** chamada . A `HighMessageFilter` regra seleciona apenas mensagens com uma propriedade personalizada `messageposition` superior a 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

O exemplo seguinte cria `mytopic` `LowMessages`uma subscrição para nome `LowMessageFilter`, com uma regra **SqlFilter** chamada . A `LowMessageFilter` regra seleciona `messageposition` apenas mensagens com uma propriedade inferior ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`Com, `HighMessages`e `LowMessages` tudo em vigor, `mytopic` as mensagens enviadas `AllMessages` são sempre entregues aos recetores da subscrição. As mensagens também são `HighMessages` entregues seletivamente para a `LowMessages` `messageposition` ou subscrição, dependendo do valor de propriedade da mensagem. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

As aplicações `send_topic_message` utilizam o método do objeto **ServiceBusService** para enviar mensagens para um tópico de ônibus de serviço.

O exemplo seguinte envia cinco `mytopic` mensagens de teste para o tópico. O `messageposition` valor de propriedade personalizado depende da iteração do loop, e determina quais as subscrições que recebem as mensagens. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites e quotas de tamanho da mensagem

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que um tópico pode conter, mas há um limite no tamanho total das mensagens que o tópico contém. Pode definir o tamanho do tópico no momento da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As aplicações `receive_subscription_message` utilizam o método no objeto **ServiceBusService** para receber mensagens de uma subscrição. O exemplo seguinte recebe `LowMessages` mensagens da subscrição e elimina-as à medida que são lidas:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

O `peek_lock` parâmetro opcional `receive_subscription_message` determina se o Service Bus elimina as mensagens da subscrição à medida que são lidas. O modo predefinido para receção `peek_lock` de mensagens é *PeekLock*, ou definido para **True**, que lê (espreita) e bloqueia mensagens sem as apagar da subscrição. Cada mensagem deve então ser explicitamente completada para removê-la da subscrição.

Para eliminar mensagens da subscrição à medida `peek_lock` que são lidas, pode definir o parâmetro para **Falso**, como no exemplo anterior. Apagar mensagens como parte da operação de receção é o modelo mais simples, e funciona bem se a aplicação puder tolerar mensagens em falta se houver uma falha. Para compreender este comportamento, considere um cenário em que a aplicação emite um pedido de receção e depois se despenhe antes de processá-lo. Se a mensagem foi apagada ao ser recebida, quando a aplicação recomeça e volta a consumir mensagens, perdeu a mensagem que recebeu antes do acidente.

Se a sua aplicação não tolerar mensagens perdidas, o recebimento torna-se uma operação em duas fases. PeekLock encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e devolve-a à aplicação. Após o processamento ou armazenamento da mensagem, a aplicação completa `complete` a segunda fase do processo de receção, ligando para o método no objeto **Mensagem.**  O `complete` método marca a mensagem como sendo consumida e retira-a da subscrição.

O exemplo que se segue demonstra um cenário de bloqueio:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com falhas de aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar uma `unlock` mensagem por alguma razão, pode ligar para o método no objeto **Mensagem.** O Service Bus desbloqueia a mensagem dentro da subscrição e disponibiliza-a para ser recebida novamente, quer pela mesma aplicação ou por outra aplicação consumista.

Há também um intervalo para mensagens bloqueadas dentro da subscrição. Se uma aplicação não processar uma mensagem antes de expirar o prazo de bloqueio, por exemplo, se a aplicação falhar, o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser recebida novamente.

Se uma aplicação falhar após o `complete` processamento de uma mensagem, mas antes de ligar para o método, a mensagem será reentregue à aplicação quando recomeçar. Este comportamento é frequentemente chamado *de Processamento pelo menos uma vez.* Cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o seu cenário não tolerar o processamento duplicado, pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega, para lidar com a entrega de mensagens duplicadas. 

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Para eliminar tópicos e subscrições, utilize `delete_topic` o [portal Azure][Azure portal] ou o método. O seguinte código elimina `mytopic`o tópico denominado:

```python
bus_service.delete_topic('mytopic')
```

A eliminação de um tópico elimina todas as subscrições do tópico. Também pode eliminar subscrições de forma independente. O seguinte código elimina `HighMessages` a `mytopic` subscrição nomeada do tópico:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Por predefinição, os tópicos e subscrições são persistentes e existem até que os elimine. Para eliminar automaticamente as subscrições após um determinado período de tempo, pode definir o parâmetro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) na subscrição. 

> [!TIP]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite-lhe ligar-se a um espaço de nome do Bus de Serviço e administrar facilmente entidades de mensagens. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais:

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Referência SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
