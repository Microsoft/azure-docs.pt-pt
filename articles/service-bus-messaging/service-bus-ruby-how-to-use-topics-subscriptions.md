---
title: 'Quickstart: Como usar tópicos de ônibus de serviço (Ruby)'
description: 'Quickstart: Saiba como usar tópicos e subscrições de ônibus de serviço em Azure. As amostras de código estão escritas para aplicações ruby.'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718942"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Quickstart: Como usar tópicos e subscrições de ônibus de serviço com Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar tópicos de ônibus de serviço e subscrições de aplicações Ruby. Os cenários abordados incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Apagar tópicos e subscrições


## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu Estúdio Visual ou subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se para uma conta [gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um espaço de **nome** de ônibus de serviço e obter a cadeia de **ligação**. 

    > [!NOTE]
    > Você vai criar um **tópico** e uma **subscrição** para o tópico usando **Ruby** neste arranque rápido. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Criar um tópico
O objeto **Azure::ServiceBusService** permite-lhe trabalhar com tópicos. O seguinte código cria um objeto **Azure::ServiceBusService.** Para criar um tópico, use o `create_topic()` método. O exemplo seguinte cria um tópico ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Também pode passar um objeto **Azure::ServiceBus::Tópico** com opções adicionais, que lhe permitem anular definições de tópicopadrão, como tempo de mensagem para viver ou tamanho máximo da fila. O exemplo seguinte mostra a definição do tamanho máximo da fila para 5 GB e tempo para viver até 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar subscrições
As subscrições de tópicos também são criadas com o objeto **Azure::ServiceBusService.** As subscrições são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da subscrição.

Por padrão, as subscrições são persistentes. Continuam a existir até que eles, ou o tópico a que estão associados, sejam eliminados. Se a sua aplicação contiver lógica para criar uma subscrição, deve primeiro verificar se a subscrição já existe utilizando o método getSubscri.

Pode ter as subscrições automaticamente eliminadas, definindo a [propriedade AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro quando for criada uma nova subscrição, o filtro **MatchAll** (predefinido) é utilizado. Quando o filtro **MatchAll** é utilizado, todas as mensagens publicadas para o tópico são colocadas na fila virtual da subscrição. O exemplo seguinte cria uma subscrição chamada "all-messages" e utiliza o filtro **MatchAll** predefinido.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que lhe permitem especificar quais as mensagens enviadas para um tópico que devem aparecer dentro de uma subscrição específica.

O tipo de filtro mais flexível suportado por subscrições é o **Azure::ServiceBus::SqlFilter,** que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, reveja a sintaxe [SqlFilter.](service-bus-messaging-sql-filter.md)

Pode adicionar filtros a uma `create_rule()` subscrição utilizando o método do objeto **Azure::ServiceBusService.** Este método permite-lhe adicionar novos filtros a uma subscrição existente.

Uma vez que o filtro predefinido é aplicado automaticamente a todas as novas subscrições, tem primeiro de remover o filtro predefinido, ou o **MatchAll** substitui quaisquer outros filtros que possa especificar. Pode remover a regra predefinida utilizando o `delete_rule()` método do objeto **Azure::ServiceBusService.**

O exemplo seguinte cria uma subscrição chamada "high-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que tenham uma propriedade personalizada `message_number` superior a 3:

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

Da mesma forma, o exemplo `low-messages` seguinte cria uma subscrição com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que tenham uma `message_number` propriedade inferior ou igual a 3:

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

Quando uma mensagem é `test-topic`agora enviada, é sempre entregue aos `all-messages` recetores subscritos pela subscrição do tópico, e entregue seletivamente aos recetores subscritos pelas subscrições `high-messages` e `low-messages` tópicos (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de `send_topic_message()` ônibus de serviço, a sua aplicação deve usar o método no objeto **Azure::ServiceBusService.** As mensagens enviadas para os tópicos do Service Bus são exemplos dos objetos **Azure::ServiceBus:::BrokeredMessage.** Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão `label` (tais como e), `time_to_live`um dicionário que é usado para conter propriedades específicas da aplicação personalizada, e um corpo de dados de cordas. Uma aplicação pode definir o corpo da mensagem `send_topic_message()` passando um valor de cadeia para o método e quaisquer propriedades padrão necessárias são povoadas por valores padrão.

O exemplo que se segue demonstra `test-topic`como enviar cinco mensagens de teste para . O `message_number` valor de propriedade personalizado de cada mensagem varia na iteração do loop (determina qual a subscrição que a recebe):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas de uma subscrição utilizando o `receive_subscription_message()` método no objeto **Azure::ServiceBusService.** Por predefinição, as mensagens são lidas (pico) e bloqueadas sem a apagar da subscrição. Pode ler e apagar a mensagem da `peek_lock` subscrição definindo a opção para **falsas**.

O comportamento padrão torna a leitura e a pagando uma operação em duas fases, o que também permite apoiar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação terminar o processamento da mensagem (ou armazená-la de forma `delete_subscription_message()` fiável para processamento futuro), completa a segunda fase do processo de receção, ligando para o método de chamada e fornecendo a mensagem a ser eliminada como parâmetro. O `delete_subscription_message()` método marca a mensagem como sendo consumida e removê-la da subscrição.

Se `:peek_lock` o parâmetro estiver definido para **falso,** ler e apagar a mensagem torna-se o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Considere um cenário em que o consumidor emite o pedido de receção e, em seguida, se despenhe antes de processá-lo. Como o Service Bus marcou a mensagem como sendo consumida, então quando a aplicação recomeça e começa a consumir mensagens novamente, perdeu a mensagem que foi consumida antes do acidente.

O exemplo que se segue demonstra como `receive_subscription_message()`as mensagens podem ser recebidas e processadas utilizando . O exemplo recebe e elimina primeiro `low-messages` uma mensagem `:peek_lock` da subscrição utilizando o conjunto `high-messages` para **falso,** depois recebe outra mensagem da mensagem e depois elimina a mensagem usando: `delete_subscription_message()`

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem `unlock_subscription_message()` por alguma razão, então pode ligar para o método no objeto **Azure::ServiceBusService.** Faz com que a Service Bus desbloqueie a mensagem dentro da subscrição e a disponibilize novamente para ser recebida, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da subscrição, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem automaticamente e torná-lo disponível para ser recebido novamente.

No caso de a aplicação se falhar `delete_subscription_message()` após o processamento da mensagem, mas antes de o método ser chamado, a mensagem é reentregue à aplicação quando reinicia. É frequentemente chamado *pelo menos uma vez processado;* ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Esta lógica é muitas `message_id` vezes alcançada usando a propriedade da mensagem, que permanece constante em todas as tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Os tópicos e subscrições são persistentes a menos que a [propriedade AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) esteja definida. Podem ser eliminados através do [portal Azure][Azure portal] ou programáticamente. O exemplo que se segue demonstra `test-topic`como eliminar o tópico denominado .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O seguinte código demonstra como eliminar `high-messages` a `test-topic` subscrição nomeada do tópico:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais.

* Ver [Filas, tópicos e subscrições.](service-bus-queues-topics-subscriptions.md)
* Referência da API para [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Visite o [Azure SDK para](https://github.com/Azure/azure-sdk-for-ruby) o repositório Ruby no GitHub.

[Azure portal]: https://portal.azure.com
