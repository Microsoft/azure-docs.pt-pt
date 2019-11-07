---
title: 'Início rápido: como usar os tópicos do barramento de serviço (Ruby)'
description: 'Início rápido: saiba como usar os tópicos e as assinaturas do barramento de serviço no Azure. Exemplos de código são escritos para aplicativos Ruby.'
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718942"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Início rápido: como usar os tópicos e as assinaturas do barramento de serviço com o Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar os tópicos e as assinaturas do barramento de serviço de aplicativos Ruby. Os cenários abordados incluem:

- Criando tópicos e assinaturas 
- Criando filtros de assinatura 
- Enviando mensagens para um tópico 
- Recebendo mensagens de uma assinatura
- Excluindo tópicos e assinaturas


## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios do assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [início rápido: Use o portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **namespace** do barramento de serviço e obter a **cadeia de conexão**. 

    > [!NOTE]
    > Você criará um **tópico** e uma **assinatura** para o tópico usando o **Ruby** neste guia de início rápido. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Criar um tópico
O objeto **Azure:: ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **Azure:: ServiceBusService** . Para criar um tópico, use o método `create_topic()`. O exemplo a seguir cria um tópico ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Você também pode passar um objeto **Azure:: ServiceBus:: topic** com opções adicionais, que permitem substituir as configurações do tópico padrão, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra a configuração do tamanho máximo da fila para 5 GB e a vida útil para um minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar assinaturas
As assinaturas de tópico também são criadas com o objeto **Azure:: ServiceBusService** . As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

Por padrão, as assinaturas são persistentes. Eles continuam existindo até que eles ou o tópico ao qual estão associados, sejam excluídos. Se seu aplicativo contiver lógica para criar uma assinatura, ele deverá primeiro verificar se a assinatura já existe usando o método getsubscription.

Você pode fazer com que as assinaturas sejam excluídas automaticamente definindo a [Propriedade AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **filtro matchall** (padrão) será usado. Quando o filtro **filtro matchall** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura chamada "All-Messages" e usa o filtro **filtro matchall** padrão.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Você também pode definir filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer em uma assinatura específica.

O tipo de filtro mais flexível com suporte das assinaturas é o **Azure:: ServiceBus:: sqlfilter**, que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [sqlfilter](service-bus-messaging-sql-filter.md) .

Você pode adicionar filtros a uma assinatura usando o método `create_rule()` do objeto **Azure:: ServiceBusService** . Esse método permite que você adicione novos filtros a uma assinatura existente.

Como o filtro padrão é aplicado automaticamente a todas as novas assinaturas, primeiro você deve remover o filtro padrão ou o **filtro matchall** substitui quaisquer outros filtros que você possa especificar. Você pode remover a regra padrão usando o método `delete_rule()` no objeto **Azure:: ServiceBusService** .

O exemplo a seguir cria uma assinatura denominada "High-messages" com um **Azure:: ServiceBus:: sqlfilter** que seleciona apenas as mensagens que têm uma propriedade de `message_number` personalizada maior que 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Da mesma forma, o exemplo a seguir cria uma assinatura chamada `low-messages` com um **Azure:: ServiceBus:: sqlfilter** que seleciona apenas as mensagens que têm uma propriedade `message_number` menor ou igual a 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Quando uma mensagem é enviada para `test-topic`, ela sempre é entregue aos destinatários inscritos na assinatura do tópico de `all-messages` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do tópico `high-messages` e `low-messages` (dependendo da mensagem conteúdo).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do barramento de serviço, seu aplicativo deve usar o método `send_topic_message()` no objeto **Azure:: ServiceBusService** . As mensagens enviadas aos tópicos do barramento de serviço são instâncias dos objetos **Azure:: ServiceBus:: BrokeredMessage** . Os objetos **Azure:: ServiceBus:: BrokeredMessage** têm um conjunto de propriedades padrão (como `label` e `time_to_live`), um dicionário que é usado para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados de cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem passando um valor de cadeia de caracteres para o método `send_topic_message()` e todas as propriedades padrão necessárias são preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `test-topic`. O `message_number` valor da propriedade personalizada de cada mensagem varia de acordo com a iteração do loop (determina qual assinatura o recebe):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
As mensagens são recebidas de uma assinatura usando o método `receive_subscription_message()` no objeto **Azure:: ServiceBusService** . Por padrão, as mensagens são lidas (pico) e são bloqueadas sem excluí-las da assinatura. Você pode ler e excluir a mensagem da assinatura definindo a opção `peek_lock` como **false**.

O comportamento padrão faz a leitura e a exclusão de uma operação de dois estágios, o que também possibilita o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método `delete_subscription_message()` e fornecendo a mensagem a ser excluída como um parâmetro. O método `delete_subscription_message()` marca a mensagem como sendo consumida e a remove da assinatura.

Se o parâmetro `:peek_lock` for definido como **false**, a leitura e a exclusão da mensagem se tornarão o modelo mais simples e funcionará melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem quando ocorrer uma falha. Considere um cenário no qual o consumidor emite a solicitação de recebimento e depois cai antes de processá-la. Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele perdeu a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando `receive_subscription_message()`. O exemplo primeiro recebe e exclui uma mensagem da assinatura `low-messages` usando `:peek_lock` definido como **false**; em seguida, ele recebe outra mensagem da `high-messages` e, em seguida, exclui a mensagem usando `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlock_subscription_message()` no objeto **Azure:: ServiceBusService** . Ele faz com que o barramento de serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e disponibilize-o para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `delete_subscription_message()` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Ele é frequentemente chamado *de processamento de pelo menos uma vez*; ou seja, cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Geralmente, essa lógica é obtida usando a propriedade `message_id` da mensagem, que permanece constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e as assinaturas são persistentes, a menos que a [Propriedade AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) esteja definida. Eles podem ser excluídos por meio do [portal do Azure][Azure portal] ou de forma programática. O exemplo a seguir demonstra como excluir o tópico chamado `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O código a seguir demonstra como excluir a assinatura denominada `high-messages` do tópico `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos dos tópicos do barramento de serviço, siga estes links para saber mais.

* Consulte [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
* Referência da API para [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Visite o repositório [SDK do Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) no github.

[Azure portal]: https://portal.azure.com
