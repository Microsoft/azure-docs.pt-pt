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
ms.openlocfilehash: 03e22c4c179850e5140015c0abc2d89f16d4b624
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774546"
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
- Uma subscrição do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um espaço de nome de ônibus de serviço, criado seguindo os passos na [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições](service-bus-quickstart-topics-subscriptions-portal.md). Copie o nome do espaço de nome, o nome da chave de acesso partilhado e o valor chave principal do ecrã de políticas de **acesso partilhado** para usar mais tarde neste arranque rápido. 
- Python 3.4x ou superior, com o pacote [Azure Python SDK][Azure Python package] instalado. Para mais informações, consulte o Guia de [Instalação Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Criar um objeto ServiceBusService

Um objeto **ServiceBusService** permite-lhe trabalhar com tópicos e subscrições de tópicos. Para aceder programáticamente ao Service Bus, adicione a seguinte linha perto do topo do seu ficheiro Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adicione o seguinte código para criar um objeto **ServiceBusService.** Substitua `<namespace>`, `<sharedaccesskeyname>`e `<sharedaccesskeyvalue>` com o nome do seu espaço de nome do Bus de Serviço, nome-chave Shared Access Signature (SAS) e valor-chave primário. Pode encontrar estes valores ao abrigo de políticas de **acesso partilhado** no seu espaço de nome sacar o seu ônibus de serviço no [portal Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Criar um tópico

O seguinte código utiliza o método `create_topic` para criar um tópico de ônibus de serviço chamado `mytopic`, com definições predefinidas:

```python
bus_service.create_topic('mytopic')
```

Pode utilizar opções de tópicos para anular as definições padrão de tópicos, tais como tempo de mensagem para viver (TTL) ou tamanho máximo do tópico. O exemplo seguinte cria um tópico chamado `mytopic` com um tamanho máximo de tópico de 5 GB e mensagem padrão TTL de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

Também utiliza o objeto **ServiceBusService** para criar subscrições a tópicos. Uma subscrição pode ter um filtro para restringir o conjunto de mensagens entregue à sua fila virtual. Se não especificar um filtro, novas subscrições utilizam o filtro **MatchAll** predefinido, que coloca todas as mensagens publicadas no tópico na fila virtual da subscrição. O exemplo seguinte cria uma subscrição de `mytopic` chamada `AllMessages` que utiliza o filtro **MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Utilize filtros com subscrições

Utilize o método `create_rule` do objeto **ServiceBusService** para filtrar as mensagens que aparecem numa subscrição. Pode especificar regras quando criar a subscrição ou adicionar regras às subscrições existentes.

O tipo de filtro mais flexível é um **SqlFilter,** que utiliza um subconjunto de SQL-92. Os filtros SQL funcionam com base nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que pode utilizar com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Uma vez que o filtro predefinido **MatchAll** se aplica automaticamente a todas as novas subscrições, tem de removê-lo das subscrições que pretende filtrar, ou **o MatchAll** sobrepor-se-á a quaisquer outros filtros que especifique. Pode remover a regra predefinida utilizando o método `delete_rule` do objeto **ServiceBusService.**

O exemplo seguinte cria uma subscrição de `mytopic` chamada `HighMessages`, com uma regra **SqlFilter** chamada `HighMessageFilter`. A regra `HighMessageFilter` seleciona apenas mensagens com uma propriedade `messageposition` personalizada superior a 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

O exemplo seguinte cria uma subscrição de `mytopic` chamada `LowMessages`, com uma regra **SqlFilter** chamada `LowMessageFilter`. A regra `LowMessageFilter` seleciona apenas mensagens com uma propriedade `messageposition` inferior ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Com `AllMessages`, `HighMessages`, e `LowMessages` tudo em vigor, as mensagens enviadas para `mytopic` são sempre entregues aos recetores da subscrição `AllMessages`. As mensagens também são entregues seletivamente à subscrição `HighMessages` ou `LowMessages`, dependendo do valor `messageposition` propriedade da mensagem. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

As aplicações utilizam o método `send_topic_message` do objeto **ServiceBusService** para enviar mensagens para um tópico de ônibus de serviço.

O exemplo seguinte envia cinco mensagens de teste para o tema `mytopic`. O valor de propriedade personalizado `messageposition` depende da iteração do loop, e determina quais as subscrições que recebem as mensagens. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites e quotas de tamanho da mensagem

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que um tópico pode conter, mas há um limite no tamanho total das mensagens que o tópico contém. Pode definir o tamanho do tópico no momento da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

As aplicações utilizam o método `receive_subscription_message` no objeto **ServiceBusService** para receber mensagens de uma subscrição. O exemplo que se segue recebe mensagens da subscrição `LowMessages` e elimina-as à medida que são lidas:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

O `peek_lock` parâmetro opcional de `receive_subscription_message` determina se o Service Bus elimina as mensagens da subscrição à medida que são lidas. O modo predefinido para receção de mensagens é *PeekLock*, ou `peek_lock` definido para **True**, que lê (espreita) e bloqueia mensagens sem as apagar da subscrição. Cada mensagem deve então ser explicitamente completada para removê-la da subscrição.

Para eliminar mensagens da subscrição à medida que são lidas, pode definir o parâmetro `peek_lock` para **Falso,** como no exemplo anterior. Apagar mensagens como parte da operação de receção é o modelo mais simples, e funciona bem se a aplicação puder tolerar mensagens em falta se houver uma falha. Para compreender este comportamento, considere um cenário em que a aplicação emite um pedido de receção e depois se despenhe antes de processá-lo. Se a mensagem foi apagada ao ser recebida, quando a aplicação recomeça e volta a consumir mensagens, perdeu a mensagem que recebeu antes do acidente.

Se a sua aplicação não tolerar mensagens perdidas, o recebimento torna-se uma operação em duas fases. PeekLock encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e devolve-a à aplicação. Após o processamento ou armazenamento da mensagem, a aplicação completa a segunda fase do processo de receção, ligando para o método `complete` no objeto **Mensagem.**  O método `complete` marca a mensagem como sendo consumida e retira-a da subscrição.

O exemplo que se segue demonstra um cenário de bloqueio:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com falhas de aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar uma mensagem por alguma razão, pode chamar o método `unlock` no objeto **Mensagem.** O Service Bus desbloqueia a mensagem dentro da subscrição e disponibiliza-a para ser recebida novamente, quer pela mesma aplicação ou por outra aplicação consumista.

Há também um intervalo para mensagens bloqueadas dentro da subscrição. Se uma aplicação não processar uma mensagem antes de expirar o prazo de bloqueio, por exemplo, se a aplicação falhar, o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser recebida novamente.

Se uma aplicação falhar após o processamento de uma mensagem, mas antes de ligar para o método `complete`, a mensagem será reentregue à aplicação quando recomeçar. Este comportamento é frequentemente chamado *de Processamento pelo menos uma vez.* Cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o seu cenário não tolerar o processamento duplicado, pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega, para lidar com a entrega de mensagens duplicadas. 

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Para eliminar tópicos e subscrições, utilize o [portal Azure][Azure portal] ou o método `delete_topic`. O seguinte código elimina o tópico denominado `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A eliminação de um tópico elimina todas as subscrições do tópico. Também pode eliminar subscrições de forma independente. O seguinte código elimina a subscrição denominada `HighMessages` do tópico `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Por predefinição, os tópicos e subscrições são persistentes e existem até que os elimine. Para eliminar automaticamente as subscrições após um determinado período de tempo, pode definir o parâmetro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) na subscrição. 

> [!TIP]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite-lhe ligar-se a um espaço de nome do Bus de Serviço e administrar facilmente entidades de mensagens. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais:

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Referência SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
