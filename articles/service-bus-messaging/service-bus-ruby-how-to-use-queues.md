---
title: 'Início rápido: como usar as filas do barramento de serviço do Azure com o Ruby'
description: 'Início rápido: saiba como usar as filas do barramento de serviço no Azure. Exemplos de código escritos em Ruby.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 09fdc58254d260b6ffeff958b6bbda50332adfac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718765"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Início rápido: como usar as filas do barramento de serviço com o Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá a criar aplicativos Ruby para enviar e receber mensagens de uma fila do barramento de serviço. Os exemplos são escritos em Ruby e usam o Azure Gem.

## <a name="prerequisites"></a>Pré-requisitos
1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) .
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando o Ruby neste tutorial. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O objeto **Azure:: ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método `create_queue()`. O exemplo a seguir cria uma fila ou imprime quaisquer erros.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Você também pode passar um objeto **Azure:: ServiceBus:: Queue** com opções adicionais, o que permite substituir as configurações de fila padrão, como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra como definir o tamanho máximo da fila para 5 GB e a vida útil para um minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do barramento de serviço, seu aplicativo chama o método `send_queue_message()` no objeto **Azure:: ServiceBusService** . As mensagens enviadas para (e recebidas de) as filas do barramento de serviço são objetos **Azure:: ServiceBus:: BrokeredMessage** e têm um conjunto de propriedades padrão (como `label` e `time_to_live`), um dicionário que é usado para manter Propriedades personalizadas específicas do aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando um valor de cadeia de caracteres como a mensagem e todas as propriedades padrão necessárias são preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila chamada `test-queue` usando `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `receive_queue_message()` no objeto **Azure:: ServiceBusService** . Por padrão, as mensagens são lidas e bloqueadas sem serem excluídas da fila. No entanto, você pode excluir mensagens da fila conforme elas são lidas, definindo a opção `:peek_lock` como **false**.

O comportamento padrão faz a leitura e a exclusão de uma operação de dois estágios, o que também possibilita o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando o método `delete_queue_message()` e fornecendo a mensagem a ser excluída como um parâmetro. O método `delete_queue_message()` marcará a mensagem como sendo consumida e a removerá da fila.

Se o parâmetro `:peek_lock` for definido como **false**, a leitura e a exclusão da mensagem se tornarão o modelo mais simples e funcionará melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Como o barramento de serviço marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como receber e processar mensagens usando `receive_queue_message()`. O exemplo primeiro recebe e exclui uma mensagem usando `:peek_lock` definido como **false**, ele recebe outra mensagem e, em seguida, exclui a mensagem usando `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlock_queue_message()` no objeto **Azure:: ServiceBusService** . Essa chamada faz com que o barramento de serviço desbloqueie a mensagem na fila e disponibilize-a para ser recebida novamente, seja pelo mesmo aplicativo de consumo ou por outro aplicativo de consumo.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem automaticamente e a tornará disponível para ser recebido novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `delete_queue_message()` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse processo é frequentemente chamado *de processamento de pelo menos uma vez*; ou seja, cada mensagem é processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isso geralmente é obtido com o uso da propriedade `message_id` da mensagem, que permanece constante nas tentativas de entrega.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas das Filas do Service Bus, siga estas hiperligações para saber mais.

* Visão geral de [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
* Visite o repositório [SDK do Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) no github.

Para obter uma comparação entre as filas do barramento de serviço do Azure discutidas neste artigo e as filas do Azure discutidas no artigo [como usar o armazenamento de filas do Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) , consulte [filas do Azure e filas do barramento de serviço do Azure – comparações e contrastes](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

