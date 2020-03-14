---
title: Mensagens azure Service Bus - filas, tópicos e subscrições
description: Este artigo fornece uma visão geral das entidades de mensagens Azure Service Bus (fila, tópicos e subscrições).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259516"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e subscrições do Service Bus

O Microsoft Azure Service Bus suporta um conjunto de tecnologias de middleware baseadas em nuvem, orientadas para mensagens, incluindo filas de mensagens fiáveis e mensagens de publicação/subscrição duradouras. Estas capacidades de mensagens "intermediadas" podem ser consideradas como funcionalidades de mensagens dissociadas que suportam a subscrição de editoras, a dissociação temporal e os cenários de equilíbrio de carga utilizando a carga de trabalho de mensagens Service Bus. A comunicação desacoplada tem muitas vantagens; por exemplo, os clientes e os servidores podem ligar-se conforme necessário e efetuar as operações de forma assíncrona.

As entidades de mensagens que formam o núcleo das capacidades de mensagens no Service Bus são filas, tópicos e subscrições, e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem a entrega de mensagens *First In, First out* (FIFO) a um ou mais consumidores concorrentes. Ou seja, os recetores normalmente recebem e processam mensagens na ordem em que foram adicionadas à fila, e apenas uma mensagem que o consumidor recebe e processa cada mensagem. Um dos principais benefícios da utilização de filas é conseguir a "dissociação temporal" dos componentes da aplicação. Por outras palavras, os produtores (remetentes) e os consumidores (recetores) não têm de enviar e receber mensagens ao mesmo tempo, porque as mensagens são armazenadas de forma duradoura na fila. Além disso, o produtor não tem de esperar por uma resposta do consumidor para continuar a processar e enviar mensagens.

Um benefício relacionado é o "nivelamento da carga", que permite aos produtores e consumidores enviar e receber mensagens a diferentes tarifas. Em muitas aplicações, a carga do sistema varia ao longo do tempo; no entanto, o tempo de processamento necessário para cada unidade de trabalho é tipicamente constante. Intermediar os produtores de mensagens e os consumidores com uma fila significa que a aplicação consumista só tem de ser provisionada para poder suportar a carga média em vez da carga máxima. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Esta capacidade economiza diretamente dinheiro no que diz respeito à quantidade de infraestrutura necessária para servir a carga da aplicação. À medida que a carga aumenta, mais processos de trabalhadores podem ser adicionados para ler a partir da fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, este equilíbrio de carga baseado em puxar permite uma utilização ótima dos computadores dos trabalhadores, mesmo que os computadores dos trabalhadores diferem no que diz respeito ao poder de processamento, uma vez que puxam mensagens à sua própria taxa máxima. Este padrão é frequentemente chamado de padrão de "consumidor concorrente".

A utilização de filas para intermediárioentre produtores de mensagens e consumidores proporciona um acoplamento inerente entre os componentes. Uma vez que os produtores e os consumidores não estão conscientes uns dos outros, um consumidor pode ser atualizado sem ter qualquer efeito sobre o produtor.

### <a name="create-queues"></a>Criar filas

Cria filas utilizando os [modelos](service-bus-resource-manager-namespace-queue.md) [do portal Azure,](service-bus-quickstart-portal.md) [PowerShell,](service-bus-quickstart-powershell.md) [CLI](service-bus-quickstart-cli.md)ou Resource Manager . Em seguida, envia e recebe mensagens usando um objeto [QueueClient.](/dotnet/api/microsoft.azure.servicebus.queueclient)

Para aprender rapidamente a criar uma fila, em seguida, enviar e receber mensagens de e para a fila, consulte os [quickstarts](service-bus-quickstart-portal.md) para cada método. Para um tutorial mais aprofundado sobre como usar as filas, consulte [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md).

Para obter uma amostra de trabalho, consulte a [amostra BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) no GitHub.

### <a name="receive-modes"></a>Receber modos

Pode especificar dois modos diferentes em que o Service Bus recebe mensagens: *ReceiveAndDelete* ou *PeekLock*. No modo [ReceiveAndDelete,](/dotnet/api/microsoft.azure.servicebus.receivemode) a operação de receção é de tiro único; isto é, quando a Service Bus recebe o pedido do consumidor, marca a mensagem como sendo consumida e devolve-a à aplicação do consumidor. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que a aplicação pode tolerar não processar uma mensagem se ocorrer uma falha. Para compreender este cenário, considere um cenário em que o consumidor emita o pedido de receção e, em seguida, cai antes de processá-lo. Como o Service Bus marca a mensagem como sendo consumida, quando a aplicação recomeça e volta a consumir mensagens, terá perdido a mensagem que foi consumida antes do acidente.

No modo [PeekLock,](/dotnet/api/microsoft.azure.servicebus.receivemode) a operação de receção torna-se em duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. Quando a Service Bus recebe o pedido, encontra a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores o recebam e, em seguida, a desconere na aplicação. Depois de a aplicação terminar o processamento da mensagem (ou armazená-la de forma fiável para processamento futuro), completa a segunda fase do processo de receção, ligando para [completeAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem recebida. Quando o Service Bus vê a chamada **CompleteAsync,** marca a mensagem como sendo consumida.

Se a aplicação não conseguir processar a mensagem por alguma razão, pode ligar para o método [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) na mensagem recebida (em vez de [CompleteAsync).](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) Este método permite à Service Bus desbloquear a mensagem e disponibilizá-la novamente, quer pelo mesmo consumidor, quer por outro consumidor concorrente. Em segundo lugar, existe um intervalo associado ao bloqueio e se a aplicação não processar a mensagem antes do prazo de bloqueio expirar (por exemplo, se a aplicação falhar), então o Service Bus desbloqueia a mensagem e disponibiliza-a para ser recebida novamente (essencialmente realizando uma operação [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) por defeito).

No caso de a aplicação falhar após o processamento da mensagem, mas antes de ser emitido o pedido **CompleteAsync,** a mensagem é retransmitida à aplicação quando recomeçar. Este processo é muitas vezes chamado de processamento *pelo menos uma vez;* isto é, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações, a mesma mensagem pode ser retransmitida. Se o cenário não puder tolerar o processamento duplicado, então é necessária uma lógica adicional na aplicação para detetar duplicados, que podem ser alcançados com base na propriedade [messageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) da mensagem, que permanece constante em todas as tentativas de entrega. Esta funcionalidade é conhecida como *Processamento Exata Uma Vez.*

## <a name="topics-and-subscriptions"></a>Tópicos e subscrições

Em contraste com as filas, nas quais cada mensagem é processada por um único consumidor, *os tópicos* e *subscrições* fornecem uma forma de comunicação de um a muitos, num padrão de *publicação/subscrição.* Útil para a escala para um grande número de destinatários, cada mensagem publicada é disponibilizada para cada subscrição registada com o tópico. As mensagens são enviadas para um tópico e entregues a uma ou mais subscrições associadas, dependendo das regras de filtro que podem ser definidas numa base por subscrição. As subscrições podem usar filtros adicionais para restringir as mensagens que querem receber. As mensagens são enviadas para um tópico da mesma forma que são enviadas para uma fila, mas as mensagens não são recebidas diretamente do tema. Em vez disso, são recebidos de assinaturas. Uma subscrição de tópico assemelha-se a uma fila virtual que recebe cópias das mensagens que são enviadas para o tema. As mensagens são recebidas de uma subscrição idêntica à forma como são recebidas de uma fila.

A título de comparação, a funcionalidade de envio de mensagens de um mapas de fila diretamente para um tópico e os seus mapas de funcionalidades de receção de mensagens para uma subscrição. Entre outras coisas, esta funcionalidade significa que as subscrições suportam os mesmos padrões descritos anteriormente nesta secção no que diz respeito às filas: consumidor concorrente, dissociação temporal, nivelamento de carga e equilíbrio de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

Criar um tópico é semelhante à criação de uma fila, como descrito na secção anterior. Em seguida, envia mensagens utilizando a classe [TopicClient.](/dotnet/api/microsoft.azure.servicebus.topicclient) Para receber mensagens, cria uma ou mais subscrições para o tema. Semelhante supor as filas, as mensagens são recebidas de uma subscrição utilizando um objeto [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) em vez de um objeto [QueueClient.](/dotnet/api/microsoft.azure.servicebus.queueclient) Crie o cliente de subscrição, passando o nome do tópico, o nome da subscrição e (opcionalmente) o modo de receção como parâmetros.

Para um exemplo completo de trabalho, consulte a [amostra BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) no GitHub.

### <a name="rules-and-actions"></a>Regras e ações

Em muitos cenários, as mensagens com características específicas devem ser processadas de diferentes formas. Para ativar este processamento, pode configurar subscrições para encontrar mensagens que tenham propriedades desejadas e, em seguida, realizar certas modificações nessas propriedades. Enquanto as subscrições do Service Bus vêem todas as mensagens enviadas para o tópico, só pode copiar um subconjunto dessas mensagens para a fila de subscrição virtual. Esta filtragem é realizada utilizando filtros de subscrição. Tais modificações são chamadas *de ações*de filtro . Quando uma subscrição é criada, pode fornecer uma expressão de filtro que opera nas propriedades da mensagem, tanto as propriedades do sistema (por exemplo, **Label)** como as propriedades de aplicação personalizadas (por exemplo, **StoreName**.) Neste caso, a expressão do filtro SQL é opcional; sem uma expressão de filtro SQL, qualquer ação de filtro definida numa subscrição será realizada em todas as mensagens para essa subscrição.

Para obter um exemplo completo de trabalho, consulte a [amostra TópicoSSubscrições ComRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no GitHub.

Para obter mais informações sobre possíveis valores de filtro, consulte a documentação para as classes [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction.](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)

## <a name="next-steps"></a>Passos seguintes

Para mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Quickstart: Enviar e receber mensagens utilizando o portal Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar o inventário utilizando o portal Azure e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)


