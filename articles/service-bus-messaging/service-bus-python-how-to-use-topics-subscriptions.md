---
title: Como utilizar os tópicos do Service bus do Azure com Python | Documentos da Microsoft
description: Saiba como utilizar tópicos de Service bus do Azure e as subscrições do Python.
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
ms.date: 09/20/2018
ms.author: aschhab
ms.openlocfilehash: 476c51d1835a1be0178faf28e6dd8a3c95371929
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240971"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Como utilizar tópicos do Service Bus e as subscrições com Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar os tópicos e as subscrições do Service Bus. Os exemplos são escritos em Python e utilize o [pacote Azure Python SDK][Azure Python package]. Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens para um tópico**, **a receber mensagens de uma subscrição**, e **eliminar tópicos e subscrições**. Para obter mais informações sobre tópicos e subscrições, veja a [passos seguintes](#next-steps) secção.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Se precisar de instalar o Python ou o [pacote de Python do Azure][Azure Python package], consulte a [guia de instalação do Python](../python-how-to-install.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-topic"></a>Criar um tópico

O **ServiceBusService** objeto permite-lhe trabalhar com tópicos. Adicione o seguinte código perto da parte superior de qualquer ficheiro de Python no qual pretende aceder através de programação do Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código seguinte cria um **ServiceBusService** objeto. Substitua `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o seu espaço de nomes real, valor de nome e chave de chave de assinatura de acesso partilhado (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Pode obter os valores para o nome da chave SAS e o valor do [portal do Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

O `create_topic` método também oferece suporte a opções adicionais, que permitem-lhe substituir as predefinições de tópico, como o tempo de live da mensagem ou tamanho de tópico máximo. O exemplo seguinte define o tamanho de tópico máximo de 5 GB e um período de tempo (TTL) valor de um minuto de TTL:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar subscrições

Subscrições de tópicos também são criadas com o **ServiceBusService** objeto. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da subscrição.

> [!NOTE]
> As assinaturas são persistentes e continuarão a existir até a eles ou tópico em que está inscrito, são eliminados.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido

Se for especificado nenhum filtro quando é criada uma nova subscrição, o **MatchAll** filtro (predefinição) é usado. Quando o **MatchAll** filtro é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição com o nome `AllMessages` e utiliza a predefinição **MatchAll** filtro.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros

Também pode definir filtros que permitem-lhe especificar que as mensagens enviadas para um tópico devem aparecer no espaço de uma subscrição de tópico específico.

O tipo mais flexível de filtro suportado pelas subscrições é um **SqlFilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais informações sobre as expressões que podem ser utilizadas com um filtro de SQL, veja a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Pode adicionar filtros a uma subscrição, utilizando o **crie\_regra** método da **ServiceBusService** objeto. Este método permite-lhe adicionar novos filtros a uma subscrição existente.

> [!NOTE]
> Uma vez que o filtro predefinido é aplicado automaticamente a todas as novas subscrições, primeiro tem de remover o filtro predefinido ou o **MatchAll** irá substituir todos os filtros que pode especificar. Pode remover a regra predefinida, utilizando o `delete_rule` método da **ServiceBusService** objeto.
> 
> 

O exemplo seguinte cria uma subscrição com o nome `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens possuem `messagenumber` propriedade superior a 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Da mesma forma, o exemplo seguinte cria uma subscrição com o nome `LowMessages` com um **SqlFilter** que seleciona apenas as mensagens com um `messagenumber` propriedade menor ou igual a 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando uma mensagem é enviada para `mytopic` será sempre entregue aos destinatários para os **AllMessages** subscrição de tópico e entregue seletivamente aos destinatários a **HighMessages**e **LowMessages** subscrições de tópicos (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem para um tópico do Service Bus, a aplicação tem de utilizar o `send_topic_message` método da **ServiceBusService** objeto.

O exemplo seguinte demonstra como enviar cinco mensagens de teste para `mytopic`. O `messagenumber` valor da propriedade de cada mensagem varia com a iteração do ciclo (Isto determina quais as subscrições que recebem a mesma):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre as quotas, consulte [quotas do Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As mensagens são recebidas a partir de uma subscrição com o `receive_subscription_message` método no **ServiceBusService** objeto:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da subscrição como eles são lidas quando o parâmetro `peek_lock` está definido como **False**. Pode ler (pré-visualização) e a mensagem de bloqueio sem eliminá-lo da fila definindo o parâmetro `peek_lock` para **True**.

O comportamento de leitura e a eliminação da mensagem como parte da operação receive é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem quando houver uma falha. Para compreender este comportamento, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-lo. Porque o Service Bus marcou a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, ele tem estado em falta a mensagem consumida antes da falha de sistema.

Se o `peek_lock` parâmetro estiver definido como **True**, a receção torna-se uma operação de duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar `delete` método no **mensagem** objeto. O `delete` método marca a mensagem como consumida e remove-o da subscrição.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis

O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, então pode chamar o `unlock` método no **mensagem** objeto. Esse método faz com que o Service Bus desbloqueie a mensagem dentro da subscrição e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na subscrição e se a aplicação conseguir processar a mensagem antes do bloqueio de tempo limite expira (por exemplo, se a falha da aplicação), em seguida, do Service Bus desbloqueia automaticamente a mensagem e torna-o disponível para ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes a `delete` método é chamado, em seguida, a mensagem será reenviada para o aplicativo quando ele for reiniciado. Este comportamento é frequentemente designado. Pelo menos uma vez processamento *; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Para tal, pode utilizar o **MessageId** propriedade da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Tópicos e subscrições são persistentes e tem de ser explicitamente eliminada através da [portal do Azure] [ Azure portal] ou programaticamente. O exemplo seguinte mostra como eliminar o tópico com o nome `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O código seguinte mostra como eliminar uma subscrição com o nome `HighMessages` partir o `mytopic` tópico:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu as noções básicas de tópicos do Service Bus, siga estas ligações para saber mais.

* Ver [filas, tópicos e subscrições][Queues, topics, and subscriptions].
* Referência para [Sqlexpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
