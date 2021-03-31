---
title: Como usar as filas de autocarros da Azure Service com a Ruby
description: Neste tutorial, você aprende a criar aplicações Ruby para enviar mensagens e receber mensagens de uma fila de ônibus de serviço.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336643"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Quickstart: Como usar as filas de autocarros de serviço com a Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprende a criar aplicações Ruby para enviar mensagens e receber mensagens de uma fila de ônibus de serviço. As amostras estão escritas em Ruby e usam a gema Azure.

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus.
    1. Leia a **visão geral** rápida das filas de **autocarros** de serviço. 
    2. Crie um espaço de nomes de **autocarro de serviço.** 
    3. Obtenha a **cadeia de ligação.** 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nomes do Service Bus usando Ruby neste tutorial. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O objeto **Azure::ServiceBusService** permite-lhe trabalhar com filas. Para criar uma fila, use o `create_queue()` método. O exemplo a seguir cria uma fila ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Também pode passar um **Azure::ServiceBus::O** objeto de fila com opções adicionais, que lhe permite anular as definições de fila predefinida, como o tempo de mensagem para viver ou o tamanho máximo da fila. O exemplo a seguir mostra como definir o tamanho máximo da fila para 5 GB e tempo para viver até 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem para uma fila de ônibus de serviço, a sua aplicação liga para `send_queue_message()` o método no **azure::ServiceBusService.** As mensagens enviadas para (e recebidas de) as filas de autocarros de serviço são **Azure::ServiceBus:::BrokeredMessage** objects, e têm um conjunto de propriedades padrão (tais como `label` `time_to_live` e), um dicionário que é usado para conter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrária. Uma aplicação pode definir o corpo da mensagem passando um valor de cadeia, uma vez que a mensagem e quaisquer propriedades padrão exigidas são povoadas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila `test-queue` `send_queue_message()` denominada:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas de uma fila utilizando o `receive_queue_message()` método no **Azure::ServiceBusService.** Por predefinição, as mensagens são lidas e bloqueadas sem serem eliminadas da fila. No entanto, pode eliminar as mensagens da fila à medida que são lidas, definindo a `:peek_lock` opção **para falso**.

O comportamento padrão faz com que a leitura e a eliminação de uma operação em duas fases, o que também permite suportar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após o fim da aplicação no processamento da mensagem (ou armazena-a de forma fiável para o processamento futuro), completa a segunda fase do processo de receção, através do método de chamada `delete_queue_message()` e fornecendo a mensagem para ser eliminada como parâmetro. O `delete_queue_message()` método marcará a mensagem como sendo consumida e removê-la-á da fila.

Se o `:peek_lock` parâmetro for definido como **falso,** ler e eliminar a mensagem torna-se o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como a Service Bus marcou a mensagem como sendo consumida, quando a aplicação recomeçar e voltar a consumir mensagens, terá perdido a mensagem que foi consumida antes do acidente.

O exemplo a seguir demonstra como receber e processar mensagens utilizando `receive_queue_message()` . O exemplo primeiro recebe e elimina uma mensagem utilizando `:peek_lock` o conjunto para **falso,** em seguida, recebe outra mensagem e, em seguida, elimina a mensagem usando `delete_queue_message()` :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem por alguma razão, então pode ligar para o `unlock_queue_message()` método no **Azure::O objeto ServiceBusService.** Esta chamada faz com que o Service Bus desbloqueie a mensagem dentro da fila e a disponibilize para ser novamente recebida, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Existe também um tempo limite associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e torna-a disponível para ser novamente recebida.

No caso de a aplicação falhar após o processamento da mensagem, mas antes do `delete_queue_message()` método ser chamado, a mensagem é reentrexada na aplicação quando reinicia. Este processo é muitas vezes chamado *pelo menos uma vez o processamento;* ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser reenrimida. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é muitas vezes conseguido usando a `message_id` propriedade da mensagem, que permanece constante em todas as tentativas de entrega.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas das Filas do Service Bus, siga estas hiperligações para saber mais.

* Visão geral de [filas, tópicos e subscrições.](service-bus-queues-topics-subscriptions.md)
* Visite o [Azure SDK para](https://github.com/Azure/azure-sdk-for-ruby) o repositório Ruby no GitHub.

Para uma comparação entre as filas de autocarros da Azure Service discutidas neste artigo e as filas Azure discutidas no como usar o armazenamento de fila a partir do artigo [da Ruby,](../storage/queues/storage-ruby-how-to-use-queue-storage.md) consulte as filas de [Azure e as filas de autocarros do Serviço Azure - Comparadas e Contrastadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

