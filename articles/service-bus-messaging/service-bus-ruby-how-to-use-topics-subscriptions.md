---
title: 'Quickstart: Como usar tópicos de Service Bus (Ruby)'
description: 'Quickstart: Saiba como usar tópicos e subscrições do Service Bus em Azure. As amostras de código são escritas para aplicações Ruby.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067567"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Quickstart: Como usar tópicos e subscrições do Service Bus com a Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar tópicos de Service Bus e subscrições de aplicações Ruby. Os cenários abrangidos incluem:

- Criação de tópicos e subscrições 
- Criação de filtros de subscrição 
- Envio de mensagens para um tópico 
- Receber mensagens de uma subscrição
- Excluir tópicos e subscrições


## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **espaço de nomes** de Service Bus e obter a **cadeia de ligação**. 

    > [!NOTE]
    > Você vai criar um **tópico** e uma **subscrição** do tópico usando **Ruby** neste arranque rápido. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Criar um tópico
O objeto **Azure::ServiceBusService** permite-lhe trabalhar com tópicos. O seguinte código cria um **Azure::ServiceBusService** objeto. Para criar um tópico, use o `create_topic()` método. O exemplo a seguir cria um tópico ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Também pode passar um **Azure::ServiceBus::Objeto com** opções adicionais, que lhe permitem substituir as definições de tópicos predefinidos, tais como o tempo de mensagem para viver ou o tamanho máximo da fila. O exemplo a seguir mostra a fixação do tamanho máximo da fila para 5 GB e o tempo para viver até 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar subscrições
As subscrições de tópicos também são criadas com o objeto **Azure::ServiceBusService.** As subscrições são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues na fila virtual da subscrição.

Por padrão, as subscrições são persistentes. Continuam a existir até que ou o tópico a que estão associados sejam eliminados. Se a sua aplicação contiver lógica para criar uma subscrição, deve primeiro verificar se a subscrição já existe utilizando o método getSubscription.

Pode eliminar automaticamente as subscrições definindo a [propriedade AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro (MatchAll) predefinido
Se não for especificado nenhum filtro quando uma nova subscrição for criada, o filtro **MatchAll** (predefinido) é utilizado. Quando o filtro **MatchAll** é utilizado, todas as mensagens publicadas no tópico são colocadas na fila virtual da subscrição. O exemplo a seguir cria uma subscrição chamada "todas as mensagens" e utiliza o filtro **MatchAll** predefinido.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com filtros
Também pode definir filtros que lhe permitam especificar quais as mensagens enviadas para um tópico que devem aparecer dentro de uma subscrição específica.

O tipo de filtro mais flexível suportado por subscrições é o **Azure::ServiceBus::SqlFilter,** que implementa um subconjunto de SQL92. Os filtros do SQL operam nas propriedades das mensagens publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, reveja a sintaxe [SqlFilter.](service-bus-messaging-sql-filter.md)

Pode adicionar filtros a uma subscrição utilizando o `create_rule()` método do **Azure::ServiceBusService.** Este método permite-lhe adicionar novos filtros a uma subscrição existente.

Uma vez que o filtro predefinido é aplicado automaticamente a todas as novas subscrições, tem primeiro de remover o filtro predefinido, ou o **MatchAll** substitui quaisquer outros filtros que possa especificar. Pode remover a regra por defeito utilizando o `delete_rule()` método no **Azure::ServiceBusService.**

O exemplo a seguir cria uma subscrição denominada "altas mensagens" com um **Azure::ServiceBus::SqlFilter** que apenas seleciona mensagens que têm uma propriedade personalizada `message_number` superior a 3:

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

Da mesma forma, o exemplo a seguir cria uma subscrição nomeada `low-messages` com um **Azure::ServiceBus::SqlFilter** que apenas seleciona mensagens que têm uma `message_number` propriedade inferior ou igual a 3:

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

Quando uma mensagem é agora enviada para `test-topic` , é sempre entregue aos recetores subscritos à subscrição de `all-messages` tópicos, e entregue seletivamente aos destinatários subscritos às `high-messages` subscrições de tópicos e `low-messages` tópicos (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico de Service Bus, a sua aplicação deve utilizar o `send_topic_message()` método no objeto **Azure::ServiceBusService.** As mensagens enviadas para os tópicos do Service Bus são exemplos do **Azure::ServiceBus:::BrokeredMessage** objects. **Azure::ServiceBus::BrokeredMessage** objects have a conjunto de propriedades padrão (tais como `label` `time_to_live` e), um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de cadeia. Uma aplicação pode definir o corpo da mensagem passando um valor de cadeia para o `send_topic_message()` método e quaisquer propriedades padrão necessárias são povoadas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `test-topic` . O `message_number` valor da propriedade personalizada de cada mensagem varia na iteração do loop (determina qual a subscrição que a recebe):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas num tópico, contudo, existe um limite do tamanho total das mensagens contidas num tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
As mensagens são recebidas a partir de uma subscrição utilizando `receive_subscription_message()` o método no **Azure::ServiceBusService.** Por predefinição, as mensagens são lidas (pico) e bloqueadas sem a eliminar da subscrição. Pode ler e eliminar a mensagem da subscrição definindo a `peek_lock` opção **falsa**.

O comportamento padrão faz com que a leitura e a eliminação de uma operação em duas fases, o que também permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após o fim da aplicação no processamento da mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, através do método de chamada `delete_subscription_message()` e fornecendo a mensagem para ser eliminada como parâmetro. O `delete_subscription_message()` método marca a mensagem como sendo consumida e removê-la da subscrição.

Se o `:peek_lock` parâmetro for definido como **falso,** ler e eliminar a mensagem torna-se o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem quando ocorre uma falha. Considere um cenário em que o consumidor emite o pedido de receção e, em seguida, cai antes de processá-lo. Como a Service Bus marcou a mensagem como sendo consumida, então quando a aplicação reinicia e volta a consumir mensagens, perdeu a mensagem que foi consumida antes do acidente.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas utilizando `receive_subscription_message()` . O exemplo primeiro recebe e elimina uma mensagem da subscrição utilizando o `low-messages` `:peek_lock` conjunto para **falso,** em seguida, recebe outra mensagem do `high-messages` e, em seguida, elimina a mensagem usando `delete_subscription_message()` :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por alguma razão, então pode ligar para o `unlock_subscription_message()` método no **Azure::O objeto ServiceBusService.** Faz com que o Service Bus desbloqueie a mensagem dentro da subscrição e a disponibilize para ser novamente recebida, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Existe também um timeout associado a uma mensagem bloqueada dentro da subscrição, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `delete_subscription_message()` método ser chamado, a mensagem é reentrexada na aplicação quando reinicia. É frequentemente chamado *pelo menos uma vez o processamento;* ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Esta lógica é muitas vezes alcançada usando a `message_id` propriedade da mensagem, que permanece constante em todas as tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições
Tópicos e subscrições são persistentes a menos que a [propriedade AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) esteja definida. Podem ser eliminados através do [portal Azure][Azure portal] ou programático. O exemplo a seguir demonstra como eliminar o tópico denominado `test-topic` .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

A eliminação de um tópico elimina também quaisquer subscrições registadas com o tópico. As subscrições também podem ser eliminadas de modo independente. O seguinte código demonstra como eliminar a subscrição nomeada `high-messages` do `test-topic` tópico:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico dos tópicos do Service Bus, siga estes links para saber mais.

* Consulte [filas, tópicos e subscrições.](service-bus-queues-topics-subscriptions.md)
* Referência da API para [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Visite o [Azure SDK para](https://github.com/Azure/azure-sdk-for-ruby) o repositório Ruby no GitHub.

[Azure portal]: https://portal.azure.com