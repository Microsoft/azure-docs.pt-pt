---
title: Como usar as filas de ônibus de serviço Azure com Ruby
description: Neste tutorial, você aprende a criar aplicações Ruby para enviar mensagens e receber mensagens de uma fila de ônibus de serviço.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760594"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Quickstart: Como usar as filas de ônibus de serviço com Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprende a criar aplicações Ruby para enviar mensagens e receber mensagens de uma fila de ônibus de serviço. As amostras estão escritas em Ruby e usam a joia Azure.

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga os passos no portal Use Azure para criar um artigo de [fila de ônibus de serviço.](service-bus-quickstart-portal.md)
    1. Leia a **visão geral** rápida das filas de **ônibus**de serviço . 
    2. Crie um espaço de **nome**de ônibus de serviço . 
    3. Pegue a corda de **ligação**. 

        > [!NOTE]
        > Você vai criar uma **fila** no espaço de nome do Ônibus de serviço usando Ruby neste tutorial. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O objeto **Azure::ServiceBusService** permite-lhe trabalhar com filas. Para criar uma fila, use o `create_queue()` método. O exemplo seguinte cria uma fila ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Também pode passar um objeto **Azure::ServiceBus::Fila** com opções adicionais, o que lhe permite anular as definições de fila padrão, como tempo de mensagem para viver ou tamanho máximo da fila. O exemplo que se segue mostra como definir o tamanho máximo da fila para 5 GB e tempo para viver até 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem para uma fila `send_queue_message()` de ônibus de serviço, a sua aplicação liga para o método do objeto **Azure::ServiceBusService.** As mensagens enviadas para (e recebidas de) filas de ônibus de serviço são **Azure::ServiceBus::BrokeredMessage** objects, e têm um conjunto de propriedades padrão (tais como `label` e `time_to_live`), um dicionário que é usado para deter propriedades personalizadas específicas da aplicação, e um corpo de dados de aplicação arbitrárias. Uma aplicação pode definir o corpo da mensagem passando um valor de cadeia, uma vez que a mensagem e quaisquer propriedades padrão necessárias são povoadas com valores padrão.

O exemplo que se segue demonstra como enviar `test-queue` `send_queue_message()`uma mensagem de teste à fila nomeada utilizando:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas de uma fila utilizando o `receive_queue_message()` método no objeto **Azure::ServiceBusService.** Por predefinição, as mensagens são lidas e bloqueadas sem serem eliminadas da fila. No entanto, pode eliminar mensagens da fila `:peek_lock` à medida que são lidas, definindo a opção de **falsas**.

O comportamento padrão torna a leitura e a pagando uma operação em duas fases, o que também permite apoiar aplicações que não podem tolerar mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação terminar o processamento da mensagem (ou armazená-la de forma `delete_queue_message()` fiável para processamento futuro), completa a segunda fase do processo de receção, ligando para o método de chamada e fornecendo a mensagem a ser eliminada como parâmetro. O `delete_queue_message()` método marcará a mensagem como sendo consumida e removê-la-á da fila.

Se `:peek_lock` o parâmetro estiver definido para **falso,** ler e apagar a mensagem torna-se o modelo mais simples, e funciona melhor para cenários em que uma aplicação pode tolerar não processar uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como a Service Bus marcou a mensagem como sendo consumida, quando a aplicação recomeça e volta a consumir mensagens, terá perdido a mensagem que foi consumida antes do acidente.

O exemplo que se segue demonstra `receive_queue_message()`como receber e processar mensagens utilizando . O exemplo recebe e elimina primeiro `:peek_lock` uma mensagem utilizando o conjunto para **falso,** depois `delete_queue_message()`recebe outra mensagem e depois elimina a mensagem usando:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não conseguir processar a mensagem `unlock_queue_message()` por alguma razão, então pode ligar para o método no objeto **Azure::ServiceBusService.** Esta chamada faz com que o Service Bus desbloqueie a mensagem dentro da fila e a disponibilize novamente para ser recebida, quer pela mesma aplicação consumista, quer por outra aplicação consumista.

Há também um intervalo associado a uma mensagem bloqueada dentro da fila, e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia automaticamente a mensagem e disponibiliza-a para ser recebida novamente.

No caso de a aplicação se falhar `delete_queue_message()` após o processamento da mensagem, mas antes de o método ser chamado, a mensagem é reentregue à aplicação quando reinicia. Este processo é muitas vezes chamado *de Pelo Menos Uma Vez Processamento;* ou seja, cada mensagem é processada pelo menos uma vez, mas em certas situações a mesma mensagem pode ser retransmitida. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é muitas `message_id` vezes conseguido usando a propriedade da mensagem, que permanece constante em todas as tentativas de entrega.

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas das Filas do Service Bus, siga estas hiperligações para saber mais.

* Visão geral das [filas, tópicos e subscrições.](service-bus-queues-topics-subscriptions.md)
* Visite o [Azure SDK para](https://github.com/Azure/azure-sdk-for-ruby) o repositório Ruby no GitHub.

Para uma comparação entre as filas de ônibus de serviço azure discutidas neste artigo e as filas Azure discutidas no como usar o armazenamento de fila a partir do artigo [da Ruby,](../storage/queues/storage-ruby-how-to-use-queue-storage.md) consulte as filas azure e as filas de ônibus de [serviço Azure - Comparado e Contrastado](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

