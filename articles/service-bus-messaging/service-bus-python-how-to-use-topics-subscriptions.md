---
title: 'Início rápido: usar os tópicos e as assinaturas do barramento de serviço do Azure com o Python'
description: Saiba como usar os tópicos e as assinaturas do barramento de serviço do Azure do Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 94a49b31139947c6323ab391b78ecd03ee911e0a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748498"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Início rápido: usar as assinaturas e os tópicos do barramento de serviço com o Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar o Python com tópicos e assinaturas do barramento de serviço do Azure. Os exemplos usam o pacote do [SDK do Python do Azure][Azure Python package] para: 

- Criar tópicos e assinaturas para tópicos
- Criar regras e filtros de assinatura
- Enviar mensagens para tópicos 
- Receber mensagens de assinaturas
- Eliminar tópicos e subscrições

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um namespace do barramento de serviço, criado seguindo as etapas em [início rápido: Use a Portal do Azure para criar um tópico e assinaturas do barramento de serviço](service-bus-quickstart-topics-subscriptions-portal.md). Copie o nome do namespace, o nome da chave de acesso compartilhado e o valor da chave primária da tela **políticas de acesso compartilhado** para usar posteriormente neste guia de início rápido. 
- Python 3.4 x ou superior, com o pacote do [SDK do Python do Azure][Azure Python package] instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Criar um objeto ServiceBusService

Um objeto **ServiceBusService** permite que você trabalhe com tópicos e assinaturas para tópicos. Para acessar programaticamente o barramento de serviço, adicione a seguinte linha próxima à parte superior do seu arquivo Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adicione o código a seguir para criar um objeto **ServiceBusService** . Substitua `<namespace>`, `<sharedaccesskeyname>`e `<sharedaccesskeyvalue>` pelo nome do namespace do barramento de serviço, nome da chave da assinatura de acesso compartilhado (SAS) e valor da chave primária. Você pode encontrar esses valores em **políticas de acesso compartilhado** no namespace do barramento de serviço no [portal do Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Criar um tópico

O código a seguir usa o método `create_topic` para criar um tópico do barramento de serviço chamado `mytopic`, com as configurações padrão:

```python
bus_service.create_topic('mytopic')
```

Você pode usar as opções de tópico para substituir as configurações de tópico padrão, como TTL (vida útil da mensagem) ou tamanho máximo do tópico. O exemplo a seguir cria um tópico chamado `mytopic` com um tamanho máximo de tópico de 5 GB e TTL de mensagem padrão de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

Você também usa o objeto **ServiceBusService** para criar assinaturas para tópicos. Uma assinatura pode ter um filtro para restringir o conjunto de mensagens entregue à sua fila virtual. Se você não especificar um filtro, as novas assinaturas usarão o filtro **filtro matchall** padrão, que coloca todas as mensagens publicadas no tópico na fila virtual da assinatura. O exemplo a seguir cria uma assinatura para `mytopic` `AllMessages` nomeado que usa o filtro **filtro matchall** :

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Usar filtros com assinaturas

Use o método `create_rule` do objeto **ServiceBusService** para filtrar as mensagens que aparecem em uma assinatura. Você pode especificar regras ao criar a assinatura ou adicionar regras a assinaturas existentes.

O tipo de filtro mais flexível é um **sqlfilter**, que usa um subconjunto de SQL-92. Os filtros SQL operam com base nas propriedades de mensagens publicadas no tópico. Para obter mais informações sobre as expressões que você pode usar com um filtro SQL, consulte a sintaxe [sqlfilter. Sqlexpression][SqlFilter.SqlExpression] .

Como o filtro padrão **filtro matchall** aplica-se automaticamente a todas as novas assinaturas, você deve removê-lo das assinaturas que deseja filtrar, ou **filtro matchall** substituirá os outros filtros que você especificar. Você pode remover a regra padrão usando o método `delete_rule` do objeto **ServiceBusService** .

O exemplo a seguir cria uma assinatura para `mytopic` chamada `HighMessages`, com uma regra **sqlfilter** denominada `HighMessageFilter`. A regra de `HighMessageFilter` seleciona apenas as mensagens com uma propriedade de `messageposition` personalizada maior que 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

O exemplo a seguir cria uma assinatura para `mytopic` chamada `LowMessages`, com uma regra **sqlfilter** denominada `LowMessageFilter`. A regra `LowMessageFilter` seleciona apenas as mensagens com uma propriedade `messageposition` menor ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Com `AllMessages`, `HighMessages`e `LowMessages` tudo em vigor, as mensagens enviadas para `mytopic` são sempre entregues aos destinatários da assinatura do `AllMessages`. As mensagens também são fornecidas seletivamente para a assinatura `HighMessages` ou `LowMessages`, dependendo do valor da propriedade `messageposition` da mensagem. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Os aplicativos usam o método `send_topic_message` do objeto **ServiceBusService** para enviar mensagens a um tópico do barramento de serviço.

O exemplo a seguir envia cinco mensagens de teste para o tópico `mytopic`. O valor da propriedade de `messageposition` personalizado depende da iteração do loop e determina quais assinaturas recebem as mensagens. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Cotas e limites de tamanho de mensagem

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite para o número de mensagens que um tópico pode conter, mas há uma limitação no tamanho total das mensagens que o tópico mantém. Você pode definir o tamanho do tópico no momento da criação, com um limite superior de 5 GB. 

Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

Os aplicativos usam o método `receive_subscription_message` no objeto **ServiceBusService** para receber mensagens de uma assinatura. O exemplo a seguir recebe mensagens da assinatura `LowMessages` e as exclui à medida que elas são lidas:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

O parâmetro opcional `peek_lock` de `receive_subscription_message` determina se o barramento de serviço exclui as mensagens da assinatura à medida que elas são lidas. O modo padrão para recebimento de mensagens é *Peeklock*, ou `peek_lock` definido como **true**, que lê (exibe) e bloqueia mensagens sem excluí-las da assinatura. Cada mensagem deve ser explicitamente concluída para removê-la da assinatura.

Para excluir mensagens da assinatura conforme elas são lidas, você pode definir o parâmetro `peek_lock` como **false**, como no exemplo anterior. A exclusão de mensagens como parte da operação de recebimento é o modelo mais simples e funcionará bem se o aplicativo puder tolerar mensagens ausentes se houver uma falha. Para entender esse comportamento, considere um cenário no qual o aplicativo emite uma solicitação de recebimento e depois falha antes de processá-lo. Se a mensagem foi excluída quando recebida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem recebida antes da falha.

Se seu aplicativo não puder tolerar mensagens perdidas, o recebimento se tornará uma operação de duas etapas. O PeekLock localiza a próxima mensagem a ser consumida, a bloqueia para impedir que outros consumidores a recebam e a retorna ao aplicativo. Depois de processar ou armazenar a mensagem, o aplicativo conclui o segundo estágio do processo de recebimento chamando o método `complete` no objeto **Message** .  O método `complete` marca a mensagem como sendo consumida e a remove da assinatura.

O exemplo a seguir demonstra um cenário de bloqueio de inspeção:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Lidar com falhas do aplicativo e mensagens ilegível

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar uma mensagem por algum motivo, ele poderá chamar o método `unlock` no objeto **Message** . O barramento de serviço desbloqueia a mensagem na assinatura e a disponibiliza para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou outro.

Também há um tempo limite para mensagens bloqueadas na assinatura. Se um aplicativo não processar uma mensagem antes de o tempo limite de bloqueio expirar, por exemplo, se o aplicativo falhar, o barramento de serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se um aplicativo falhar após o processamento de uma mensagem, mas antes de chamar o método `complete`, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse comportamento é geralmente chamado de *processamento de pelo menos uma vez*. Cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se seu cenário não puder tolerar o processamento duplicado, você poderá usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega, para lidar com a entrega de mensagens duplicada. 

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Para excluir tópicos e assinaturas, use o [portal do Azure][Azure portal] ou o método `delete_topic`. O código a seguir exclui o tópico chamado `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A exclusão de um tópico exclui todas as assinaturas para o tópico. Você também pode excluir assinaturas de forma independente. O código a seguir exclui a assinatura chamada `HighMessages` do tópico `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Por padrão, os tópicos e as assinaturas são persistentes e existem até você excluí-los. Para excluir automaticamente as assinaturas após um determinado período de tempo decorrido, você pode definir o parâmetro [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) na assinatura. 

> [!TIP]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que você se conecte a um namespace do barramento de serviço e administre facilmente as entidades de mensagens. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu os conceitos básicos dos tópicos do barramento de serviço, siga estes links para saber mais:

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* Referência de [sqlfiltro. Sqlexpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
