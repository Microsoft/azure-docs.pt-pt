---
title: Mensagens de autocarro da Azure Service - filas, tópicos e subscrições
description: Este artigo fornece uma visão geral das entidades de mensagens Azure Service Bus (fila, tópicos e subscrições).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b8fb68509ad920fc6911290377f49b89ec610b58
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096337"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e subscrições do Service Bus
A Azure Service Bus suporta um conjunto de tecnologias de middleware baseadas em nuvem e orientadas para mensagens, incluindo mensagens de mensagens fiáveis e mensagens de publicação/subscrição duradouras. Estas capacidades de mensagens intermediadas podem ser consideradas como funcionalidades de mensagens dissociadas que suportam cenários de subscrição, dissociação temporal e equilíbrio de cargas utilizando a carga de trabalho de mensagens Service Bus. A comunicação dissociada tem muitas vantagens. Por exemplo, os clientes e servidores podem conectar-se conforme necessário e fazer as suas operações de forma assíncronea.

As entidades de mensagens que formam o núcleo das capacidades de mensagens no Service Bus são **filas,** **tópicos e subscrições** e regras/ações.

## <a name="queues"></a>Filas
As filas oferecem entrega de mensagens **First In, First out** (FIFO) a um ou mais consumidores concorrentes. Ou seja, os recetores normalmente recebem e processam mensagens na ordem em que foram adicionados à fila. E, apenas uma mensagem que o consumidor recebe e processa cada mensagem. Um dos principais benefícios da utilização das filas é conseguir a **dissociação temporal dos componentes da aplicação.** Por outras palavras, os produtores (remetentes) e os consumidores (recetores) não têm de enviar e receber mensagens ao mesmo tempo. Isso é porque as mensagens são armazenadas duravelmente na fila. Além disso, o produtor não tem de esperar que uma resposta do consumidor continue a processar e a enviar mensagens.

Um benefício relacionado é **o nivelamento da carga,** que permite aos produtores e consumidores enviar e receber mensagens a taxas diferentes. Em muitas aplicações, a carga do sistema varia ao longo do tempo. No entanto, o tempo de processamento necessário para cada unidade de trabalho é tipicamente constante. A intermediação dos produtores de mensagens e dos consumidores com uma fila significa que a aplicação consumista só tem de ser capaz de suportar a carga média em vez da carga máxima. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Esta capacidade poupa diretamente dinheiro no que diz respeito à quantidade de infraestrutura necessária para o serviço da carga da aplicação. À medida que a carga aumenta, mais processos de trabalho podem ser adicionados para ler a partir da fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, este equilíbrio de carga baseado em puxar permite uma melhor utilização dos computadores operários, mesmo que os computadores operários com poder de processamento puxem mensagens à sua taxa máxima. Este padrão é frequentemente denominado padrão de **consumidor concorrente**.

A utilização de filas para intermediários entre os produtores de mensagens e os consumidores proporciona um acoplamento solto inerente entre os componentes. Como os produtores e os consumidores não estão conscientes uns dos outros, um consumidor pode ser atualizado sem ter qualquer efeito sobre o produtor.

### <a name="create-queues"></a>Criar filas
Pode criar filas utilizando os [modelos](service-bus-resource-manager-namespace-queue.md) [do portal Azure,](service-bus-quickstart-portal.md) [PowerShell,](service-bus-quickstart-powershell.md) [CLI](service-bus-quickstart-cli.md)ou Gestor de Recursos . Em seguida, envie e receba mensagens usando clientes escritos em [C#](service-bus-dotnet-get-started-with-queues.md), [Java,](service-bus-java-how-to-use-queues.md) [Python,](service-bus-python-how-to-use-queues.md) [JavaScript,](service-bus-nodejs-how-to-use-queues.md) [PHP](service-bus-php-how-to-use-queues.md)e [Ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Receber modos
Pode especificar dois modos diferentes em que o Service Bus recebe mensagens.

- **Receber e apagar**. Neste modo, quando a Service Bus recebe o pedido do consumidor, marca a mensagem como sendo consumida e devolve-a à aplicação do consumidor. Este modo é o modelo mais simples. Funciona melhor para cenários em que a aplicação pode tolerar não processar uma mensagem se ocorrer uma falha. Para compreender este cenário, considere um cenário em que o consumidor emite o pedido de receber e depois cai antes de o processar. À medida que o Service Bus marca a mensagem como sendo consumida, a aplicação começa a consumir mensagens no reinício. Vai perder a mensagem que consumiu antes do acidente.
- **Trave de espreitar.** Neste modo, a operação de receção torna-se em duas fases, o que permite suportar aplicações que não podem tolerar mensagens em falta. 
    1. Encontra a próxima mensagem a ser consumida, **bloqueia-a** para evitar que outros consumidores a recebam e, em seguida, devolva a mensagem à aplicação. 
    1. Após o final da aplicação terminar o processamento da mensagem, solicita ao serviço Service Bus que complete a segunda fase do processo de receção. Em seguida, o serviço **marca a mensagem como sendo consumida.** 

        Se a aplicação não conseguir processar a mensagem por algum motivo, pode solicitar ao serviço service Bus que **abandone** a mensagem. A Service Bus **desbloqueia** a mensagem e disponibiliza-a para ser novamente recebida, quer pelo mesmo consumidor, quer por outro consumidor concorrente. Em segundo lugar, há um **tempo limite** associado à fechadura. Se a aplicação não processar a mensagem antes do prazo de bloqueio expirar, o Service Bus desbloqueia a mensagem e disponibiliza-a para ser novamente recebida.

        Se a aplicação falhar depois de processar a mensagem, mas antes de solicitar ao serviço Service Bus para completar a mensagem, a Service Bus reentra a mensagem para a aplicação quando reinicia. Este processo é muitas vezes chamado **pelo menos uma vez processado.** Ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações, a mesma mensagem pode ser reenviada. Se o seu cenário não puder tolerar o processamento duplicado, adicione lógica adicional na sua aplicação para detetar duplicados. Para obter mais informações, consulte [a deteção de Duplicado.](duplicate-detection.md) Esta funcionalidade é conhecida **como exatamente uma vez** processada.

        > [!NOTE]
        > Para obter mais informações sobre estes dois modos, consulte [as operações de liquidação](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Tópicos e subscrições
Uma fila permite o processamento de uma mensagem por um único consumidor. Em contraste com as filas, tópicos e subscrições fornecem uma forma de comunicação de um a muitos num padrão **de publicação e subscrição.** É útil para escalar para um grande número de destinatários. Cada mensagem publicada é disponibilizada a cada subscrição registada com o tema. O editor envia uma mensagem para um tópico e um ou mais subscritores recebem uma cópia da mensagem, dependendo das regras de filtro definidas nestas subscrições. As subscrições podem usar filtros adicionais para restringir as mensagens que querem receber. Os editores enviam mensagens para um tópico da mesma forma que enviam mensagens para uma fila. Mas os consumidores não recebem mensagens diretamente do tema. Em vez disso, os consumidores recebem mensagens de subscrições do tema. Uma subscrição de tópico assemelha-se a uma fila virtual que recebe cópias das mensagens que são enviadas para o tema. Os consumidores recebem mensagens de uma subscrição idêntica à forma como recebem mensagens de uma fila.

A funcionalidade de envio de mensagens de uma fila mapeia diretamente para um tópico e os seus mapas de funcionalidades de receção de mensagens para uma subscrição. Entre outras coisas, esta funcionalidade significa que as subscrições suportam os mesmos padrões descritos anteriormente nesta secção no que diz respeito às filas: consumidor concorrente, dissociação temporal, nivelamento de carga e equilíbrio de carga.

### <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições
Criar um tópico é semelhante à criação de uma fila, como descrito na secção anterior. Pode criar tópicos e subscrições utilizando os modelos [do portal Azure,](service-bus-quickstart-topics-subscriptions-portal.md) [PowerShell,](service-bus-quickstart-powershell.md) [CLI](service-bus-tutorial-topics-subscriptions-cli.md)ou [Gestor de Recursos.](service-bus-resource-manager-namespace-topic.md) Em seguida, envie mensagens para um tópico e receba mensagens de subscrições utilizando clientes escritos em [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java,](service-bus-java-how-to-use-topics-subscriptions.md) [Python,](service-bus-python-how-to-use-topics-subscriptions.md) [JavaScript,](service-bus-nodejs-how-to-use-topics-subscriptions.md) [PHP](service-bus-php-how-to-use-topics-subscriptions.md)e [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Regras e ações
Em muitos cenários, as mensagens com características específicas devem ser processadas de diferentes formas. Para ativar este processamento, pode configurar subscrições para encontrar mensagens que tenham propriedades desejadas e, em seguida, realizar certas modificações a essas propriedades. Enquanto as subscrições do Service Bus vêem todas as mensagens enviadas para o tópico, só é possível copiar um subconjunto dessas mensagens para a fila de subscrição virtual. Esta filtragem é realizada com filtros de subscrição. Tais modificações são chamadas **ações de filtro**. Quando uma subscrição é criada, pode fornecer uma expressão de filtro que funciona nas propriedades da mensagem. As propriedades podem ser tanto as propriedades do sistema (por exemplo, **Label)** como as propriedades de aplicação personalizadas (por exemplo, **Nome de Loja**.) A expressão do filtro SQL é opcional neste caso. Sem uma expressão de filtro SQL, qualquer ação de filtro definida numa subscrição será feita em todas as mensagens para essa subscrição.

Para obter um exemplo completo de trabalho, consulte a [amostra TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) no GitHub.

Para obter mais informações sobre possíveis valores de filtro, consulte a documentação para as classes [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) e [SqlRuleAction.](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)

## <a name="java-message-service-jms-20-entities"></a>Serviço de mensagens Java (JMS) 2.0 entidades
As seguintes entidades estão acessíveis através do serviço de mensagens Java (JMS) 2.0 API.

  * Filas temporárias
  * Tópicos temporários
  * Assinaturas duradouras partilhadas
  * Assinaturas duradouras não partilhadas
  * Assinaturas partilhadas não duráveis
  * Assinaturas não partilhadas não duráveis

Saiba mais sobre as [entidades JMS 2.0](java-message-service-20-entities.md) e sobre como [usá-las.](how-to-use-java-message-service-20.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
* [Início Rápido: Enviar e receber mensagens com o portal do Azure e o .NET](service-bus-quickstart-portal.md)
* [Tutorial: atualizar inventário utilizando o portal do Azure e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)


