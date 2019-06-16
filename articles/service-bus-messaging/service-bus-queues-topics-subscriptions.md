---
title: Descrição geral do sistema de mensagens de filas, tópicos e subscrições do Azure Service Bus | Documentos da Microsoft
description: Descrição geral das entidades de mensagens do Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125819"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e subscrições do Service Bus

Microsoft Azure Service Bus suporta um conjunto de tecnologias de middleware baseada na cloud, orientado a mensagens, incluindo o enfileiramento de mensagens confiáveis e durável de publicação/subscrição de mensagens. Estas capacidades de mensagens "mediadas" podem ser consideradas como dissociado funcionalidades de mensagens que o suporte de publicação-subscrição, desacoplamento temporal e com a carga de trabalho de mensagens do Service Bus de cenários de balanceamento de carga. A comunicação desacoplada tem muitas vantagens; por exemplo, os clientes e os servidores podem ligar-se conforme necessário e efetuar as operações de forma assíncrona.

As entidades de mensagens que formam a base das capacidades de mensagens no Service Bus são filas, tópicos e subscrições e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem *primeiro a entrar, primeiro limite* entrega de mensagens (FIFO) para um ou mais consumidores concorrentes. Ou seja, recetores normalmente recebem e processam mensagens na ordem em que foram adicionadas à fila, e apenas um consumidor de mensagens recebe e processa cada mensagem. Das principais vantagens de utilizar as filas é obter "desacoplamento temporal" de componentes da aplicação. Em outras palavras, os produtores (remetentes) e os consumidores (recetores) não tem de enviar e receber mensagens ao mesmo tempo, uma vez que as mensagens são armazenadas de maneira duradoura na fila. Além disso, o produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e enviar mensagens.

Uma vantagem relacionada é "nivelamento de carga," que permite aos produtores e consumidores enviar e receber mensagens a taxas diferentes. Em muitos aplicativos, a carga de sistema varia ao longo do tempo; No entanto, o tempo de processamento necessário para cada unidade de trabalho é geralmente constante. A intermediação de mensagens de produtores e consumidores com uma fila significa que a aplicação de consumo apenas tem de ser aprovisionada para ser capaz de lidar com a carga média em vez de pico de carga. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Esse recurso poupa diretamente dinheiro em relação a quantidade de infraestrutura necessária para a manutenção da carga da aplicação. À medida que aumenta a carga, mais processos de trabalho podem ser adicionados a leitura da fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, este balanceamento de carga baseado na solicitação permite uma utilização ideal dos computadores de trabalho, mesmo se os computadores de trabalho são diferentes em relação à potência de processamento, como extraem as mensagens em sua própria taxa máxima. Este padrão é frequentemente denominado padrão de "consumidor competindo".

Utilizar filas para intermediário entre mensagem produtores e consumidores fornece um inerente menor rigidez entre os componentes. Uma vez que os produtores e consumidores não têm conhecimento de si, um consumidor pode ser atualizado sem ter qualquer efeito sobre o produtor.

### <a name="create-queues"></a>Criar filas

Criar filas com o [portal do Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), ou [modelos do Resource Manager](service-bus-resource-manager-namespace-queue.md). Em seguida, enviar e receber mensagens com uma [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objeto.

Para saber rapidamente como criar uma fila, em seguida, enviar e receber mensagens de e para a fila, consulte a [inícios Rápidos](service-bus-quickstart-portal.md) para cada método. Para consultar um tutorial mais aprofundado sobre como utilizar as filas de mensagens em fila, consulte [introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md).

Para obter um exemplo de trabalho, consulte a [BasicSendReceiveUsingQueueClient exemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) no GitHub.

### <a name="receive-modes"></a>Receber modos

Pode especificar dois modos diferentes em que o Service Bus recebe mensagens: *ReceiveAndDelete* ou *PeekLock*. Na [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) modo, a operação receive é única; ou seja, quando o Service Bus recebe o pedido, ele marca a mensagem como consumida e devolve a mesma à aplicação. **ReceiveAndDelete** modo é o modelo mais simples e funciona melhor para cenários em que a aplicação pode tolerar o não processamento de uma mensagem se ocorrer uma falha. Para compreender este cenário, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-lo. Uma vez que o Service Bus marca a mensagem como consumida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha de sistema.

Na [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) modo, a operação de receção torna-se duas etapas, que torna possível para suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe o pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Depois da aplicação concluir o processamento da mensagem (ou armazena-lo de forma fiável para processamento futuro), ele conclui a segunda etapa do processo de receção ao chamar [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) à mensagem recebida. Quando o Service Bus vê a **CompleteAsync** chamada, ela marca a mensagem como consumida.

Se a aplicação não consegue processar a mensagem por algum motivo, pode chamar o [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) método à mensagem recebida (em vez de [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Este método permite que o Service Bus desbloqueie a mensagem e disponibilizá-lo ser novamente recebida, pelo mesmo consumidor ou por outra consumidor concorrente. Em segundo lugar, há um tempo limite associado com o bloqueio e se a aplicação conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), Service Bus desbloqueia a mensagem e torna-o disponível para ser novamente recebida ( essencialmente a efetuar uma [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) operação por predefinição).

No caso de falha da aplicação depois de processar a mensagem, mas antes a **CompleteAsync** solicitação é emitida, a mensagem é reenviada para a aplicação quando esta reiniciar. Este processo é, muitas vezes, denominado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não pode tolerar o processamento duplicado, em seguida, a lógica adicional é necessário no aplicativo para detetar duplicados, que pode ser obtido com base na [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) propriedade da mensagem, que permanece constante em tentativas de entrega. Esta funcionalidade é conhecida como *exatamente uma vez* de processamento.

## <a name="topics-and-subscriptions"></a>Tópicos e subscrições

Em contraste com filas, em que cada mensagem é processada por um único consumidor *tópicos* e *subscrições* oferecem uma forma de um-para-muitos de comunicação, num *depublicação/subscrição* padrão. Útil para dimensionar para um grande número de destinatários, cada mensagem publicada é disponibilizada para cada subscrição registada juntamente com o tópico. As mensagens são enviadas para um tópico e entregues a um ou mais subscrições associadas, consoante as regras de filtro que podem ser definidas numa base por subscrição. As subscrições podem utilizar filtros adicionais para restringir as mensagens que pretende receber. As mensagens são enviadas para um tópico da mesma forma que eles são enviados para uma fila, mas as mensagens não são recebidas do tópico diretamente. Em vez disso, eles são recebidos a partir de subscrições. Uma subscrição de tópico é semelhante a uma fila virtual que recebe cópias das mensagens que são enviadas para o tópico. As mensagens são recebidas de uma subscrição de forma idêntica à forma como eles são recebidos a partir de uma fila.

Por meio de comparação, a funcionalidade de envio de mensagens de uma fila é mapeado diretamente para um tópico e sua funcionalidade de receção de mensagem mapeia para uma subscrição. Entre outras coisas, esta funcionalidade significa que as subscrições suportam os mesmos padrões descritos anteriormente nesta secção em relação a filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

Criar um tópico é semelhante a criar uma fila, conforme descrito na secção anterior. , Em seguida, envia mensagens utilizando o [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) classe. Para receber mensagens, crie uma ou mais subscrições para o tópico. Semelhante às filas, as mensagens são recebidas de uma subscrição a utilizar um [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objeto em vez de um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objeto. Crie a subscrição de cliente, passando o nome do tópico, o nome da subscrição e (opcionalmente) o modo de receber como parâmetros.

Para um completo trabalhar de exemplo, consulte a [BasicSendReceiveUsingTopicSubscriptionClient exemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) no GitHub.

### <a name="rules-and-actions"></a>Regras e ações

Em muitos cenários, as mensagens com características específicas devem ser processadas de formas diferentes. Para ativar este processamento, pode configurar subscrições para localizar mensagens de que tem propriedades pretendidas e, em seguida, executam algumas modificações para essas propriedades. Embora subscrições do Service Bus ver todas as mensagens enviadas para o tópico, só poderá copiar um subconjunto dessas mensagens na fila virtual da subscrição. Esta filtragem é efetuada a utilizar os filtros de subscrição. São chamadas de tais modificações *filtrar ações*. Quando é criada uma subscrição, pode fornecer uma expressão de filtro que opera nas propriedades da mensagem, ambas as propriedades do sistema (por exemplo, **rótulo**) e propriedades de aplicativo personalizado (por exemplo,  **StoreName**.) A expressão de filtro SQL é opcional neste caso; sem uma expressão de filtro SQL, qualquer ação de filtro definida numa assinatura será efetuada em todas as mensagens para essa subscrição.

Para um completo trabalhar de exemplo, consulte a [TopicSubscriptionWithRuleOperationsSample exemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no GitHub.

Para obter mais informações sobre os valores de filtro possíveis, consulte a documentação para o [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) classes.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações e exemplos de como utilizar mensagens do Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Quickstart: Enviar e receber mensagens com o portal do Azure e o .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar inventário com o portal do Azure e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)


