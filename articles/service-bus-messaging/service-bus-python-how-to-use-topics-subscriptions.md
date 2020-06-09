---
title: 'Quickstart: Use tópicos e subscrições de ônibus da Azure Service com Python'
description: Este artigo mostra-lhe como criar um tópico de Autocarro de Serviço Azure, subscrição, enviar mensagens para um tópico e receber mensagens de subscrição.
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
ms.custom: tracking-python
ms.openlocfilehash: ed2bf757762beafda3d4b2958438672c03d8d234
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560051"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Quickstart: Use tópicos e subscrições de Service Bus com Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar python com tópicos e subscrições do Azure Service Bus. As amostras utilizam o pacote [Azure Python SDK][Azure Python package] para: 

- Criar tópicos e subscrições para tópicos
- Criar filtros e regras de subscrição
- Enviar mensagens para tópicos 
- Receber mensagens de subscrições
- Eliminar tópicos e subscrições

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Pode ativar os [benefícios do seu subscritor Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Um espaço de nomes de Service Bus, criado seguindo os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições](service-bus-quickstart-topics-subscriptions-portal.md). Copie o nome do espaço de nome, o nome da chave de acesso partilhado e o valor principal das políticas de **acesso partilhado** para utilizar mais tarde neste arranque rápido. 
- Python 3.4x ou superior, com o pacote [Azure Python SDK][Azure Python package] instalado. Para mais informações, consulte o [Guia de Instalação Python.](/azure/developer/python/azure-sdk-install)

## <a name="create-a-servicebusservice-object"></a>Criar um objeto ServiceBusService

Um objeto **ServiceBusService** permite-lhe trabalhar com tópicos e subscrições de tópicos. Para aceder programáticamente service bus, adicione a seguinte linha perto do topo do seu ficheiro Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adicione o seguinte código para criar um objeto **ServiceBusService.** Substitua `<namespace>` , e pelo nome do seu espaço de nome Service `<sharedaccesskeyname>` `<sharedaccesskeyvalue>` Bus, nome de chave Assinatura de Acesso Partilhado (SAS) e valor principal da chave. Pode encontrar estes valores de acordo com **as políticas de acesso partilhado** no seu espaço de nomes de Service Bus no portal [Azure.][Azure portal]

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Criar um tópico

O seguinte código utiliza o `create_topic` método para criar um tópico de Service Bus chamado , com `mytopic` definições predefinidos:

```python
bus_service.create_topic('mytopic')
```

Pode utilizar opções de tópicos para substituir as definições de tópicos predefinidos, como o tempo de mensagem para viver (TTL) ou o tamanho máximo do tópico. O exemplo a seguir cria um tópico nomeado `mytopic` com um tamanho máximo de tópico de 5 GB e mensagem padrão TTL de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar subscrições

Também utiliza o objeto **ServiceBusService** para criar subscrições de tópicos. Uma subscrição pode ter um filtro para restringir o conjunto de mensagens entregue na sua fila virtual. Se não especificar um filtro, as novas subscrições utilizam o filtro **MatchAll** predefinido, que coloca todas as mensagens publicadas no tópico na fila virtual da subscrição. O exemplo a seguir cria uma subscrição com `mytopic` o nome que utiliza o filtro `AllMessages` **MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Use filtros com subscrições

Utilize o `create_rule` método do objeto **ServiceBusService** para filtrar as mensagens que aparecem numa subscrição. Pode especificar regras quando criar a subscrição ou adicionar regras às subscrições existentes.

O tipo de filtro mais flexível é um **SqlFilter,** que utiliza um subconjunto de SQL-92. Os filtros SQL funcionam com base nas propriedades das mensagens publicadas no tópico. Para obter mais informações sobre as expressões que pode utilizar com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Como o filtro padrão **MatchAll** se aplica automaticamente a todas as novas subscrições, tem de removê-lo das subscrições que pretende filtrar, ou **o MatchAll** irá substituir quaisquer outros filtros especificados. Pode remover a regra por defeito utilizando o `delete_rule` método do objeto **ServiceBusService.**

O exemplo a seguir cria uma subscrição com `mytopic` o nome , com uma regra `HighMessages` **SqlFilter** chamada `HighMessageFilter` . A `HighMessageFilter` regra seleciona apenas mensagens com uma propriedade personalizada `messageposition` superior a 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

O exemplo a seguir cria uma subscrição com `mytopic` o nome , com uma regra `LowMessages` **SqlFilter** chamada `LowMessageFilter` . A `LowMessageFilter` regra seleciona apenas mensagens com uma `messageposition` propriedade inferior ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Com `AllMessages` `HighMessages` , e tudo em `LowMessages` efeito, as mensagens `mytopic` enviadas são sempre entregues aos recetores da `AllMessages` subscrição. As mensagens também são entregues seletivamente na `HighMessages` ou `LowMessages` subscrição, dependendo do valor da propriedade da `messageposition` mensagem. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

As aplicações utilizam o `send_topic_message` método do objeto **ServiceBusService** para enviar mensagens para um tópico de Service Bus.

O exemplo a seguir envia cinco mensagens de teste para o `mytopic` tema. O valor da propriedade personalizada `messageposition` depende da iteração do loop e determina quais as subscrições que recebem as mensagens. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites e quotas do tamanho da mensagem

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que um tópico pode conter, mas há um limite para o tamanho total das mensagens que o tópico contém. Pode definir o tamanho do tópico na hora da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As aplicações utilizam o `receive_subscription_message` método no objeto **ServiceBusService** para receber mensagens de uma subscrição. O exemplo a seguir recebe mensagens da `LowMessages` subscrição e elimina-as à medida que são lidas:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

O parâmetro opcional `peek_lock` de determinar se o Service Bus elimina as `receive_subscription_message` mensagens da subscrição à medida que são lidas. O modo predefinido para receber mensagens é *PeekLock*, ou `peek_lock` definido para **True**, que lê (espreita) e bloqueia mensagens sem as eliminar da subscrição. Cada mensagem deve então ser explicitamente concluída para removê-la da subscrição.

Para eliminar as mensagens da subscrição à medida que são lidas, pode definir o `peek_lock` parâmetro para **Falso**, como no exemplo anterior. Eliminar mensagens como parte da operação de receção é o modelo mais simples, e funciona bem se a aplicação pode tolerar mensagens em falta se houver uma falha. Para compreender este comportamento, considere um cenário em que a aplicação emite um pedido de receção e, em seguida, cai antes de processá-lo. Se a mensagem foi apagada ao ser recebida, quando a aplicação reinicia e volta a consumir mensagens, perdeu a mensagem que recebeu antes do acidente.

Se a sua aplicação não pode tolerar mensagens perdidas, a receção torna-se uma operação em duas fases. O PeekLock encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e a devolve à aplicação. Após o processamento ou armazenamento da mensagem, a aplicação completa a segunda fase do processo de receção, chamando o `complete` método no objeto **Mensagem.**  O `complete` método marca a mensagem como sendo consumida e remove-a da subscrição.

O exemplo a seguir demonstra um cenário de bloqueio de espreitar:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Manuseia falhas de aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não puder processar uma mensagem por alguma razão, pode ligar para o `unlock` método no objeto **Mensagem.** O Service Bus desbloqueia a mensagem dentro da subscrição e disponibiliza-a para ser novamente recebida, seja pela mesma ou por outra aplicação consumista.

Há também um tempo limite para mensagens bloqueadas dentro da subscrição. Se uma aplicação não processar uma mensagem antes do prazo de bloqueio expirar, por exemplo, se a aplicação falhar, o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

Se uma aplicação falhar após o processamento de uma mensagem, mas antes de ligar para `complete` o método, a mensagem será reenrimida à aplicação quando reiniciar. Este comportamento é muitas vezes chamado *de Processamento pelo menos uma vez.* Cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o seu cenário não puder tolerar o processamento duplicado, pode utilizar a propriedade **MessageId** da mensagem, que permanece constante em todas as tentativas de entrega, para lidar com a entrega de mensagens duplicadas. 

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Para eliminar tópicos e subscrições, utilize o [portal Azure][Azure portal] ou o `delete_topic` método. O seguinte código elimina o tópico denominado `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

Excluir um tópico elimina todas as subscrições do tema. Também pode eliminar as subscrições de forma independente. O seguinte código elimina a subscrição nomeada `HighMessages` do `mytopic` tópico:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Por padrão, os tópicos e subscrições são persistentes e existem até que os elimine. Para eliminar automaticamente as subscrições após um determinado período de tempo, pode definir o parâmetro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) na subscrição. 

> [!TIP]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite-lhe ligar-se a um espaço de nomes de Service Bus e administrar facilmente entidades de mensagens. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais:

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Referência sqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
