---
title: Mensagens do barramento de serviço do Azure – filas, tópicos e assinaturas
description: Visão geral das entidades de mensagens do barramento de serviço.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 9ac8d95dda392bd3f2a438389f5f6aa434b8a2fa
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772147"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e subscrições do Service Bus

O Barramento de Serviço do Microsoft Azure dá suporte a um conjunto de tecnologias de middleware orientadas a mensagens baseadas em nuvem, incluindo enfileiramento de mensagens confiável e mensagens de publicação/assinatura duráveis. Esses recursos de mensagens "orientadas" podem ser considerados como recursos de mensagens dissociados que dão suporte a cenários de publicação-assinatura, desacoplamento temporal e balanceamento de carga usando a carga de trabalho de mensagens do barramento de serviço. A comunicação desacoplada tem muitas vantagens; por exemplo, os clientes e os servidores podem ligar-se conforme necessário e efetuar as operações de forma assíncrona.

As entidades de mensagens que formam o núcleo dos recursos de mensagens no barramento de serviço são filas, tópicos e assinaturas e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem entrega de mensagem PEPS ( *primeiro a entrar, primeiro a sair* ) para um ou mais consumidores concorrentes. Ou seja, os destinatários normalmente recebem e processam mensagens na ordem em que foram adicionados à fila, e apenas um consumidor de mensagem recebe e processa cada mensagem. Um dos principais benefícios do uso de filas é obter o "desacoplamento temporal" dos componentes do aplicativo. Em outras palavras, os produtores (remetentes) e os consumidores (receptores) não precisam enviar e receber mensagens ao mesmo tempo, porque as mensagens são armazenadas permanentemente na fila. Além disso, o produtor não precisa esperar por uma resposta do consumidor para continuar a processar e enviar mensagens.

Um benefício relacionado é "nivelamento de carga", que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes. Em muitos aplicativos, a carga do sistema varia ao longo do tempo; no entanto, o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. A intermediação de produtores de mensagens e consumidores com uma fila significa que o aplicativo de consumo só precisa ser provisionado para poder lidar com a carga média em vez da carga de pico. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Esse recurso economiza dinheiro diretamente em relação à quantidade de infraestrutura necessária para atender à carga do aplicativo. À medida que a carga aumenta, mais processos de trabalho podem ser adicionados para leitura na fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o uso ideal dos computadores de trabalho, mesmo que os computadores de trabalho sejam diferentes em relação à capacidade de processamento, pois eles efetuam pull de mensagens em sua própria taxa máxima. Esse padrão geralmente é chamado de padrão de "consumidor concorrente".

O uso de filas para intermediário entre produtores de mensagem e consumidores fornece um acoplamento flexível inerente entre os componentes. Como produtores e consumidores não estão cientes uns dos outros, um consumidor pode ser atualizado sem ter nenhum efeito no produtor.

### <a name="create-queues"></a>Criar filas

Você cria filas usando os modelos [portal do Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)ou [Resource Manager](service-bus-resource-manager-namespace-queue.md). Em seguida, você envia e recebe mensagens usando um objeto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) .

Para aprender rapidamente como criar uma fila, enviar e receber mensagens de e para a fila, consulte os guias de [início rápido](service-bus-quickstart-portal.md) para cada método. Para obter um tutorial mais aprofundado sobre como usar as filas do, consulte Introdução [às filas do barramento de serviço](service-bus-dotnet-get-started-with-queues.md).

Para obter um exemplo funcional, consulte o [exemplo BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) no github.

### <a name="receive-modes"></a>Modos de recebimento

Você pode especificar dois modos diferentes em que o barramento de serviço recebe mensagens: *ReceiveAndDelete* ou *Peeklock*. No modo [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) , a operação Receive é única; ou seja, quando o barramento de serviço recebe a solicitação do consumidor, ele marca a mensagem como sendo consumida e a retorna ao aplicativo consumidor. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem se ocorrer uma falha. Para entender esse cenário, considere um cenário no qual o consumidor emite a solicitação de recebimento e depois falha antes de processá-la. Como o barramento de serviço marca a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo [Peeklock](/dotnet/api/microsoft.azure.servicebus.receivemode) , a operação Receive se torna duas etapas, o que torna possível dar suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o barramento de serviço recebe a solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia-a para impedir que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem recebida. Quando o barramento de serviço vê a chamada **CompleteAsync** , ele marca a mensagem como sendo consumida.

Se o aplicativo não puder processar a mensagem por algum motivo, ele poderá chamar o método [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) na mensagem recebida (em vez de [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Esse método permite que o barramento de serviço desbloqueie a mensagem e torne-a disponível para ser recebida novamente, pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um tempo limite associado ao bloqueio e, se o aplicativo não processar a mensagem antes de expirar o tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o barramento de serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente (essencialmente executando uma operação [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) por padrão).

Caso o aplicativo falhe após o processamento da mensagem, mas antes que a solicitação **CompleteAsync** seja emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse processo é frequentemente chamado *de processamento de pelo menos uma vez* ; ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações, a mesma mensagem pode ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, será necessária lógica adicional no aplicativo para detectar duplicatas, o que pode ser obtido com base na propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) da mensagem, que permanece constante nas tentativas de entrega. Esse recurso é conhecido como processamento *exatamente uma vez* .

## <a name="topics-and-subscriptions"></a>Tópicos e subscrições

Em contraste com as filas, nas quais cada mensagem é processada por um único consumidor, os *Tópicos* e as *assinaturas* fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura* . Útil para o dimensionamento para grandes números de destinatários, cada mensagem publicada é disponibilizada para cada assinatura registrada com o tópico. As mensagens são enviadas a um tópico e entregues a uma ou mais assinaturas associadas, dependendo das regras de filtro que podem ser definidas em uma base por assinatura. As assinaturas podem usar filtros adicionais para restringir as mensagens que desejam receber. As mensagens são enviadas a um tópico da mesma forma que são enviadas para uma fila, mas as mensagens não são recebidas diretamente do tópico. Em vez disso, eles são recebidos de assinaturas. Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens que são enviadas para o tópico. As mensagens são recebidas de uma assinatura de forma idêntica à maneira como são recebidas de uma fila.

Por meio de comparação, a funcionalidade de envio de mensagens de uma fila é mapeada diretamente para um tópico e sua funcionalidade de recebimento de mensagens é mapeada para uma assinatura. Entre outras coisas, esse recurso significa que as assinaturas dão suporte aos mesmos padrões descritos anteriormente nesta seção em relação às filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

A criação de um tópico é semelhante à criação de uma fila, conforme descrito na seção anterior. Em seguida, você envia mensagens usando a classe [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) . Para receber mensagens, você cria uma ou mais assinaturas para o tópico. Semelhante às filas, as mensagens são recebidas de uma assinatura usando um objeto [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) em vez de um objeto [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) . Crie o cliente de assinatura, passando o nome do tópico, o nome da assinatura e (opcionalmente) o modo de recebimento como parâmetros.

Para obter um exemplo funcional completo, consulte o [exemplo BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) no github.

### <a name="rules-and-actions"></a>Regras e ações

Em muitos cenários, as mensagens que têm características específicas devem ser processadas de maneiras diferentes. Para habilitar esse processamento, você pode configurar assinaturas para localizar mensagens com propriedades desejadas e, em seguida, executar determinadas modificações nessas propriedades. Enquanto as assinaturas do barramento de serviço veem todas as mensagens enviadas ao tópico, você só pode copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Essa filtragem é realizada usando filtros de assinatura. Essas modificações são chamadas de *ações de filtro*. Quando uma assinatura é criada, você pode fornecer uma expressão de filtro que opere nas propriedades da mensagem, ambas as propriedades do sistema (por exemplo, **rótulo**) e propriedades de aplicativo personalizadas (por exemplo, **StoreName**). A expressão de filtro SQL é opcional nesse caso; sem uma expressão de filtro SQL, qualquer ação de filtro definida em uma assinatura será executada em todas as mensagens para essa assinatura.

Para obter um exemplo funcional completo, consulte o [exemplo TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no github.

Para obter mais informações sobre possíveis valores de filtro, consulte a documentação para as classes [sqlfilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [sqlruleaction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) .

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos de como usar o sistema de mensagens do barramento de serviço, consulte os seguintes tópicos avançados:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Início rápido: enviar e receber mensagens usando o portal do Azure e o .NET](service-bus-quickstart-portal.md)
* [Tutorial: atualizar o inventário usando portal do Azure e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)


