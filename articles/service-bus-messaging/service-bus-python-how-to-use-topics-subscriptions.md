---
title: Como usar os tópicos do barramento de serviço do Azure com Python | Microsoft Docs
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: d294ceaaf77175a3010131b18864b71c7b26b88b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360826"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Como usar os tópicos e as assinaturas do barramento de serviço com o Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar os tópicos e as subscrições do Service Bus. Os exemplos são escritos em Python e usam o [pacote do SDK do Python do Azure][Azure Python package]. Os cenários abordados incluem:

- Criando tópicos e assinaturas 
- Criando filtros de assinatura 
- Enviando mensagens para um tópico 
- Recebendo mensagens de uma assinatura
- Excluindo tópicos e assinaturas

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [início rápido: Use o portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **namespace** do barramento de serviço e obter a **cadeia de conexão**.

    > [!NOTE]
    > Você criará um **tópico** e uma **assinatura** para o tópico usando o **python** neste guia de início rápido. 
3. Instale o [pacote do Azure Python][Azure Python package]. Consulte o [Guia de instalação do Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite que você trabalhe com tópicos. Adicione o código a seguir próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o barramento de serviço programaticamente:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código a seguir cria um objeto **ServiceBusService** . Substitua `mynamespace`, `sharedaccesskeyname` e`sharedaccesskey` por seu namespace real, nome da chave SAS (assinatura de acesso compartilhado) e valor de chave.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Você pode obter os valores para o nome da chave SAS e o valor da [portal do Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

O `create_topic` método também dá suporte a opções adicionais, que permitem que você substitua as configurações de tópico padrão, como a vida útil da mensagem ou o tamanho máximo do tópico. O exemplo a seguir define o tamanho máximo do tópico como 5 GB e um valor TTL (vida útil) de um minuto:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar assinaturas

As assinaturas para tópicos também são criadas com o objeto **ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

> [!NOTE]
> Por padrão, as assinaturas são persistentes e continuarão existindo até que elas, ou o tópico no qual elas são assinadas, sejam excluídas.
> 
> Você pode fazer com que as assinaturas sejam excluídas automaticamente definindo a [Propriedade auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido

Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **filtro matchall** (padrão) será usado. Quando o filtro **filtro matchall** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura `AllMessages` chamada e usa o filtro **filtro matchall** padrão.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros

Você também pode definir filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer em uma assinatura de tópico específica.

O tipo de filtro mais flexível com suporte das assinaturas é um **sqlfilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que podem ser utilizadas com um filtro do SQL, veja a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Você pode adicionar filtros a uma assinatura usando o método **Create\_Rule** do objeto **ServiceBusService** . Esse método permite que você adicione novos filtros a uma assinatura existente.

> [!NOTE]
> Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, primeiro você deve remover o filtro padrão ou o **filtro matchall** substituirá quaisquer outros filtros que você possa especificar. Você pode remover a regra padrão usando o `delete_rule` método do objeto **ServiceBusService** .
> 
> 

O exemplo a seguir cria uma assinatura `HighMessages` chamada com um sqlfilter que seleciona apenas as mensagens que têm `messagenumber` uma propriedade personalizada maior que 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Da mesma forma, o exemplo a seguir cria `LowMessages` uma assinatura chamada com um sqlfilter que seleciona apenas as `messagenumber` mensagens que têm uma propriedade menor ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando uma mensagem é enviada a `mytopic` ela sempre é entregue aos destinatários inscritos na assinatura do tópico de todas as **mensagens** e entregue seletivamente aos destinatários inscritos no tópico **HighMessages** e **LowMessages** assinaturas (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico do barramento de serviço, seu aplicativo deve `send_topic_message` usar o método do objeto **ServiceBusService** .

O exemplo a seguir demonstra como enviar cinco mensagens de teste `mytopic`para. O `messagenumber` valor da propriedade de cada mensagem varia de acordo com a iteração do loop (isso determina quais assinaturas o receberão):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte cotas do [barramento de serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

As mensagens são recebidas de uma assinatura `receive_subscription_message` usando o método no objeto **ServiceBusService** :

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são excluídas da assinatura à medida que são lidas `peek_lock` quando o parâmetro é definido como **false**. Você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila definindo o parâmetro `peek_lock` como **true**.

O comportamento de ler e excluir a mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando há uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite a solicitação de recebimento e depois falha antes de processá-la. Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem que foi consumida antes da falha.

Se o `peek_lock` parâmetro for definido como **true**, o Receive se tornará uma operação de duas etapas, o que possibilitará o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de `delete` recebimento chamando o método no objeto de **mensagem** . O `delete` método marca a mensagem como sendo consumida e a remove da assinatura.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o `unlock` método no objeto **Message** . Esse método faz com que o barramento de serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e torna-o disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes de o `delete` método ser chamado, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse comportamento é geralmente chamado. Pelo menos uma vez\*processada; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Para fazer isso, você pode usar a propriedade **MessageId** da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Os tópicos e as assinaturas são persistentes, a menos que a [Propriedade auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) esteja definida. Eles podem ser excluídos por meio do [portal do Azure][Azure portal] ou de forma programática. O exemplo a seguir mostra como excluir o tópico chamado `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O código a seguir mostra como excluir uma assinatura denominada `HighMessages` `mytopic` do tópico:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu os conceitos básicos dos tópicos do barramento de serviço, siga estes links para saber mais.

* Consulte [filas, tópicos e assinaturas][Queues, topics, and subscriptions].
* Referência para [sqlfilter.][SqlFilter.SqlExpression]sqlexpression.

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
